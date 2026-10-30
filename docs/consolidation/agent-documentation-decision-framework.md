# Agent Documentation Consolidation: Decision Framework

**For:** ls-claude-agents-router architectural review  
**Date:** October 29, 2026  
**Audience:** LightSpeed engineering & product leadership

---

## Executive Summary (One-Page Stakeholder Brief)

The ls-claude-agents-router ecosystem (69 agents across 11 domains) faces a maintainability challenge: agent metadata is scattered across multiple sources, creating version conflicts and discovery friction. This framework addresses 6 critical decisions that will shape how agents are documented, installed, and evolved.

**The three pillars of our recommendation:**

1. **Version authority** resides in `.github/agents/AGENT.md` (canonical source); router stubs are generated, never manually edited
2. **Optional MCPs** are declared in agent metadata, validated at runtime (not install time) to support graceful degradation
3. **Complexity metadata** enables users to self-select agents by expertise level, reducing cognitive load for new personas

**Timeline:** Implement in two phases—Phase 1 (weeks 1-2) establishes version authority and simplification; Phase 2 (weeks 3-4) adds MCPs, complexity tiers, and governance.

**Risk mitigation:** All breaking changes (deprecation, router simplification) include a 6-week grace period and migration guide.

---

## Decision Matrix: 6 Questions

### 1. Version Authority: Single Source of Truth

**Rationale**  
When `AGENT.md` and `claude/agent.md` have different versions (e.g., 1.0.1 vs 1.0.2), users see stale metadata, agents fail to load, and maintainers waste time reconciling conflicts. A single authoritative source eliminates ambiguity and enables consistent plugin discovery.

**Options**

| Option | How It Works | Trade-offs |
|--------|-------------|-----------|
| **A. `.github/agents/AGENT.md` is canonical** (RECOMMENDED) | One file per agent in the central repo is the source of truth. Router `.agent.md` stubs are auto-generated via CI. No manual edits to router files. | Requires CI pipeline setup (~8 hours). Steep learning curve for maintainers unfamiliar with the pattern. All updates funnel through one PR process. |
| **B. Both sources co-exist, must match** | Maintain both files; CI fails if versions diverge. Manual sync required. | Cognitive overhead: two places to edit. Still creates merge conflicts. False sense of control—CI tells you what's broken, not how to fix it. |
| **C. `.github/agents/AGENT.md` is canonical, router files optional** | Central repo is source; router stubs are optional polish (deleted if outdated). Agents load from central repo only. | Breaks the "thin wrapper" pattern. Vendors copying `.agent.md` files directly into their workflows get out-of-sync copies. |

**Recommendation: Option A**

One authoritative source in `.github/agents/AGENT.md`, auto-generated router stubs. This is the standard pattern for plugin marketplaces (npm packages, VS Code extensions) and matches team familiarity.

**Implementation Example**

```yaml
# .github/agents/linear-advisor/AGENT.md
---
name: linear-advisor
version: 1.0.2
description: "Triage Linear issues with context-aware suggestions"
domains: [product-management]
required_mcps: [linear-api]  # NEW: declares dependency
complexity: intermediate      # NEW: user persona hint
deprecation_date: null
change_authority: engineering-team
---

# Snippet: CI workflow that generates router stub
# .github/workflows/sync-agents.yml
- name: Generate agent stubs
  run: |
    for file in agents/*/AGENT.md; do
      agent_name=$(basename $(dirname $file))
      generate_router_stub "$file" "router/agents/${agent_name}.agent.md"
    done
- name: Fail if router files were manually edited
  run: git diff --name-only | grep "router/agents/" && exit 1 || true
```

**Dependencies**  
→ Depends on CI/CD infrastructure (GitHub Actions available)  
→ Enables **Router Simplification** (Q2) by auto-generating stubs  
→ Blocks manual changes to router files; change authority (Q6) flows through `.github/agents/`

---

### 2. Router Simplification: Auto-Generate or Keep Stubs?

**Rationale**  
Router stubs (`ls-claude-agents-router/agents/*.agent.md`) currently duplicate metadata from the central repo. If stubs are auto-generated (depending on Q1), we reduce redundancy. If kept as manual files, they risk going stale. Simplifying the router means one less place to maintain and faster discovery for end users.

**Options**

| Option | How It Works | Trade-offs |
|--------|-------------|-----------|
| **A. Auto-generate, eliminate manual stubs** (RECOMMENDED) | CI generates `.agent.md` from `.github/agents/AGENT.md` on each commit. Router directory becomes read-only. | Requires completing Q1 (version authority). No fallback if CI breaks. Users must pull latest router to see new agents. |
| **B. Keep thin stubs, sync via scheduled job** | Stubs remain; a nightly sync job pulls metadata from central repo. Stubs are "computed" but readable. | Added operational overhead (another job to monitor). Still two representations of truth if a stub is edited by hand. |
| **C. Hybrid: generated stubs + override capability** | Stubs are generated but can be hand-edited for special cases (e.g., custom descriptions). CI warns if overrides exist. | Complexity increases: special cases become implicit. Hard to explain why some agents have overrides. |

**Recommendation: Option A**

Auto-generate stubs and treat the router directory as read-only. This aligns with Q1 (single authority) and forces all metadata changes through the central repo and its review process.

**Implementation Example**

```bash
# .github/workflows/sync-agents.yml (triggered on push to agents/*/AGENT.md)
sync-agents:
  runs-on: ubuntu-latest
  steps:
    - uses: actions/checkout@v4
      with:
        ref: ${{ github.head_ref }}
    - name: Generate agent stubs from central repo
      run: |
        python scripts/generate_stubs.py \
          --source-dir .github/agents \
          --output-dir router/agents \
          --validate-schema agent.schema.json
    - name: Commit auto-generated stubs (if changed)
      run: |
        if git diff --quiet router/agents/; then
          echo "No changes."
        else
          git config user.name "ls-agents-bot"
          git config user.email "bot@lightspeedwp.agency"
          git add router/agents/
          git commit -m "chore: auto-sync agent stubs from .github/agents"
          git push
        fi
```

**Dependencies**  
→ Requires Q1 complete (version authority in `.github/agents/AGENT.md`)  
→ Enables faster plugin discovery (users see latest agents without manual edits)  
→ Depends on CI pipeline reliability; blocks manual router edits (enforced by Q6)

---

### 3. Required MCPs: Declare and Validate?

**Rationale**  
Some agents need external MCPs (e.g., `linear-advisor` needs `linear-api`). Declaring this upfront helps users understand dependencies and prevents runtime failures. The question is whether to validate at install time (blocking install if MCP is missing) or at runtime (graceful degradation).

**Options**

| Option | How It Works | Trade-offs |
|--------|-------------|-----------|
| **A. Declare in metadata, validate at runtime** (RECOMMENDED) | `required_mcps: [linear-api]` in AGENT.md. At agent load time, check availability; fail gracefully with a helpful error. | Users may discover issues after install. Requires robust error messaging. Can lead to "why doesn't my agent work?" support tickets. |
| **B. Declare and validate at install time** | Same declaration. Install-time check: if MCP missing, block install + suggest `mcp setup`. | Tight UX: users get immediate feedback. Breaks install for users who want to install agent but configure MCP later. Over-strict for optional MCPs. |
| **C. No declaration, let agents fail** | Don't declare MCPs; if agent needs it, it fails at runtime with generic error. | Users have no warning. Poor discovery. High friction. Not recommended. |

**Recommendation: Option A**

Declare in metadata, validate at runtime. This follows the principle of "fail late, fail gracefully." Users see a clear error message pointing to the missing MCP and a link to MCP docs. This works well for both mandatory and optional MCPs.

**Implementation Example**

```yaml
# .github/agents/linear-advisor/AGENT.md
---
name: linear-advisor
version: 1.0.2
description: Triage Linear issues with context-aware suggestions
required_mcps:
  - linear-api: "^1.0"  # SemVer constraint, optional
optional_mcps:
  - slack-api: "For posting summaries to Slack"
---

# Plugin load-time check (TypeScript)
async function loadAgent(agentName: string) {
  const agent = await fetchAgent(agentName);
  const missingMcps = [];
  
  for (const mcp of agent.required_mcps || []) {
    if (!isMcpAvailable(mcp.name)) {
      missingMcps.push(mcp.name);
    }
  }
  
  if (missingMcps.length > 0) {
    throw new AgentError(
      `Agent "${agentName}" requires MCPs: ${missingMcps.join(", ")}. ` +
      `Install them with: mcp setup. Learn more: https://docs.lightspeed.ai/mcps`
    );
  }
  
  return agent;
}
```

**Dependencies**  
→ Requires Q1 complete (metadata lives in central AGENT.md)  
→ Informs plugin marketplace UI (show MCP badges on agent cards)  
→ Affects user onboarding and error messaging (Q5 deprecation policy benefits from this pattern)

---

### 4. Complexity Metadata: Beginner/Intermediate/Advanced?

**Rationale**  
Users have varying expertise: new personas exploring Claude Code (beginner) vs. teams deeply familiar with agents (advanced). A complexity field helps users self-select agents that match their skill level, reducing cognitive load and support overhead.

**Options**

| Option | How It Works | Trade-offs |
|--------|-------------|-----------|
| **A. Simple three-tier: beginner/intermediate/advanced** (RECOMMENDED) | Each agent tagged with one tier. Marketplace filters/sorts by tier. New users see beginner agents first. | Subjectivity: who decides tier? Requires guidelines (e.g., "requires <3 MCPs = beginner"). May feel patronizing. |
| **B. No complexity metadata** | Skip the field. Users infer complexity from description. | Higher friction: users must read descriptions to judge fit. More support tickets from frustrated users trying agents they're not ready for. |
| **C. Multi-dimensional: tier + prerequisites + use-case tags** | E.g., `complexity: intermediate`, `prerequisites: ["MCP understanding"]`, `use_cases: [triage, automation]`. | Richer but complex. Metadata bloat. Harder to maintain consistency. |

**Recommendation: Option A**

Adopt a simple three-tier system with clear guidelines. Guidelines are posted in CONTRIBUTING.md and enforced during PR review.

**Implementation Example**

```yaml
# .github/agents/linear-advisor/AGENT.md
complexity: intermediate

# CONTRIBUTING.md (excerpt)
## Complexity Tiers

- **Beginner**: Uses only Claude's built-in tools. No MCPs. Clear, single-purpose goal.
  Example: summarizer, code-reviewer
- **Intermediate**: Uses 1-2 MCPs. Requires context about external systems (Linear, Slack).
  Typical: workflow automation, triage
- **Advanced**: Uses 3+ MCPs or requires custom configuration. Assumes deep system knowledge.
  Example: multi-system orchestrator, custom build coordinator

# Marketplace UI (TypeScript)
function renderAgentCard(agent) {
  const tierColors = {
    beginner: 'green',
    intermediate: 'amber',
    advanced: 'red'
  };
  return (
    <div>
      <h3>{agent.name}</h3>
      <Badge color={tierColors[agent.complexity]}>
        {agent.complexity}
      </Badge>
      <p>{agent.description}</p>
    </div>
  );
}
```

**Dependencies**  
→ Requires Q1 complete (metadata field added to AGENT.md schema)  
→ Supports user discovery and onboarding (especially important for LightSpeed customer segment)  
→ Can be extended later with prerequisites or use-case tags (no breaking change)

---

### 5. Deprecation Policy: Handoff and Redirection?

**Rationale**  
Agents get deprecated as they're replaced (e.g., `linear-advisor` v1 → v2). The question is how to handle users still relying on the old agent: warn them, auto-redirect, or force migration?

**Options**

| Option | How It Works | Trade-offs |
|--------|-------------|-----------|
| **A. Deprecation warning + 6-week grace period, no auto-redirect** (RECOMMENDED) | Add `deprecation_date` and `successor_agent` fields. Agent still loads but shows a warning banner. Migration guide provided in docs. After 6 weeks, agent is archived (can be restored). | Requires user action. Some users ignore warnings and face surprises. More support work upfront. |
| **B. Auto-redirect to successor agent** | If user tries to load deprecated agent, silently load successor instead. | Breaks user workflows if successor has different inputs/outputs. Difficult to debug if redirect is unknown. Users may not notice behavioral changes. |
| **C. Immediate removal** | Delete deprecated agent; users must migrate manually. | Abrupt; high churn. Bad for customers using older agents. Breaks existing workflows without notice. |

**Recommendation: Option A**

Deprecation warnings + grace period + migration guide. No auto-redirect (respects user intent). This gives teams time to update while still maintaining the old agent.

**Implementation Example**

```yaml
# .github/agents/linear-advisor-v1/AGENT.md
---
name: linear-advisor-v1
version: 1.5.0
deprecation_date: "2026-12-31"
deprecation_reason: "Replaced by linear-advisor-v2 with improved context handling"
successor_agent: linear-advisor-v2
migration_guide_url: "https://docs.lightspeed.ai/agents/linear-advisor-migration"
---

# Plugin load logic (TypeScript)
async function loadAgent(agentName: string) {
  const agent = await fetchAgent(agentName);
  
  if (agent.deprecation_date) {
    const daysLeft = Math.ceil(
      (new Date(agent.deprecation_date) - Date.now()) / (1000 * 60 * 60 * 24)
    );
    if (daysLeft > 0) {
      console.warn(
        `⚠️ Agent "${agentName}" is deprecated and will be archived in ${daysLeft} days. ` +
        `Migrate to "${agent.successor_agent}": ${agent.migration_guide_url}`
      );
    } else {
      throw new AgentError(
        `Agent "${agentName}" was archived on ${agent.deprecation_date}. ` +
        `Use "${agent.successor_agent}" instead.`
      );
    }
  }
  
  return agent;
}

# Migration guide template (docs/agents/linear-advisor-migration.md)
# Migrating from linear-advisor-v1 to v2

## What changed
- Input: `issue_key` → `issue_id` (numeric)
- Output: `suggestions` now includes `severity` field

## Step-by-step migration
1. Update your agent call: `issue_key: "LIN-123"` → `issue_id: 123`
2. Test in dev environment
3. Update production workflows

## Support
Questions? Post in #engineering on Slack.
```

**Dependencies**  
→ Requires Q1 complete (metadata lives in AGENT.md)  
→ Requires Q6 complete (change authority for deprecation decisions)  
→ May trigger complexity tier updates (v2 agents often have different tiers)  
→ Blocks agent archival workflow (only archived after grace period)

---

### 6. Change Authority: Who Updates AGENT.md?

**Rationale**  
As agents proliferate (69 and growing), governance becomes critical. Without clear change authority, metadata drifts, versions bump without review, and the plugin marketplace becomes unreliable. This question establishes who can propose, review, and approve changes.

**Options**

| Option | How It Works | Trade-offs |
|--------|-------------|-----------|
| **A. Review required, SemVer for version bumps** (RECOMMENDED) | All AGENT.md changes require PR review from `engineering-team` (CODEOWNERS). Versions follow SemVer: patch (bug fixes), minor (new features), major (breaking changes). | Slows velocity slightly (review overhead). Requires SemVer discipline. But catches mistakes early. |
| **B. Open contribution with post-hoc review** | Anyone can bump versions and edit metadata; automated checks flag risky changes (e.g., major version bump without changelog). | Faster but riskier. Requires strong CI validation. Harder to track who approved what. |
| **C. Strict: only designated "agent-maintainer" role** | One or two senior engineers approve all changes. Centralizes quality. | Bottleneck: slow velocity, single point of failure. Doesn't scale to 69 agents and growing. |

**Recommendation: Option A**

Review required + SemVer discipline. This balances velocity and quality. Add a CODEOWNERS file that maps agents to domain experts.

**Implementation Example**

```yaml
# .github/CODEOWNERS
agents/linear-* @linear-team
agents/wordpress-* @wordpress-platform
agents/testing-* @qa-team

# .github/agents/linear-advisor/AGENT.md (snippet)
---
name: linear-advisor
version: 1.0.2  # MUST follow SemVer
maintainers:
  - @alice (Linear expert)
  - @bob (Plugin maintainer)
change_authority: engineering-team
---

# PR template for agent changes (.github/pull_request_template.md, excerpt)
## Checklist for Agent Metadata Changes
- [ ] Version bumped according to SemVer
- [ ] CHANGELOG updated (if version bump)
- [ ] Tests pass (if implementation changes)
- [ ] Assigned to domain expert for review

# CI check: SemVer validation (Node.js script)
const semver = require('semver');

function validateAgentVersion(oldVersion, newVersion, changeType) {
  const nextVersions = {
    patch: semver.inc(oldVersion, 'patch'),
    minor: semver.inc(oldVersion, 'minor'),
    major: semver.inc(oldVersion, 'major')
  };
  
  if (newVersion !== nextVersions[changeType]) {
    throw new Error(
      `Invalid version bump. Expected ${nextVersions[changeType]} for ${changeType} change, got ${newVersion}`
    );
  }
}
```

**Dependencies**  
→ Requires Q1 complete (metadata centralized in AGENT.md)  
→ Enables Q5 (deprecation policy: only approved maintainers can deprecate)  
→ Blocks unreviewed version bumps (CI will fail)  
→ Informs onboarding (new agent authors must understand SemVer and CODEOWNERS)

---

## Dependency Map

```
Question 1: Version Authority
├─ Enables Q2 (Router Simplification)
├─ Enables Q3 (Required MCPs)
├─ Enables Q4 (Complexity Metadata)
├─ Enables Q5 (Deprecation Policy)
└─ Enables Q6 (Change Authority)

Question 2: Router Simplification
├─ Depends on Q1
└─ Enables Q3, Q4, Q5, Q6 (faster CI/discovery)

Question 3: Required MCPs
├─ Depends on Q1
├─ Informs Q4 (complexity tier may depend on MCP count)
└─ Supports Q5 (MCPs may be deprecated)

Question 4: Complexity Metadata
├─ Depends on Q1
├─ Informs user discovery (independent of Q3, Q5, Q6)
└─ May affect Q6 review criteria (advanced agents need stricter review)

Question 5: Deprecation Policy
├─ Depends on Q1 (metadata), Q6 (authority to deprecate)
└─ Informs Q3 (MCPs can be deprecated too)

Question 6: Change Authority
├─ Depends on Q1
└─ Enables Q5 (enforces deprecation decisions)
```

---

## Summary: Recommended Approach

| Question | Recommendation | Timeline | Owner |
|----------|-----------------|----------|-------|
| Q1: Version Authority | Central `.github/agents/AGENT.md` is authoritative | Week 1 | Engineering Lead |
| Q2: Router Simplification | Auto-generate stubs; router is read-only | Week 1–2 | DevOps |
| Q3: Required MCPs | Declare in metadata; validate at runtime (graceful error) | Week 3 | Plugin Team |
| Q4: Complexity Metadata | Three-tier system (beginner/intermediate/advanced) | Week 3 | Product |
| Q5: Deprecation Policy | 6-week grace period; migration guides; no auto-redirect | Week 4 | Engineering Lead |
| Q6: Change Authority | Review required (CODEOWNERS); SemVer discipline | Weeks 1–2 | Engineering Lead |

**All decisions assume:**
- Version bumps follow SemVer
- Metadata is stored in AGENT.md (YAML front matter)
- CI validates schema and enforces rules
- Users are notified of changes via release notes or plugin marketplace UI

---

## Implementation Checklist (Phase 1 & 2)

### Phase 1: Version Authority & Router Simplification (Weeks 1–2)

**Week 1:**
- [ ] Audit current AGENT.md files in `.github/agents/` (establish baseline)
- [ ] Document version authority decision in CONTRIBUTING.md
- [ ] Create `.github/workflows/sync-agents.yml` for auto-generating stubs
- [ ] Write `scripts/generate_stubs.py` (or equivalent for your stack)
- [ ] Define `agent.schema.json` (validate AGENT.md structure)
- [ ] Set up CI validation: fail if router files are edited manually

**Week 2:**
- [ ] Run sync workflow on all existing agents; commit auto-generated stubs
- [ ] Update CODEOWNERS to route agent changes to domain experts
- [ ] Create migration guide: "How to contribute an agent"
- [ ] Add read-only flag to router directory (lint rule or branch protection)
- [ ] Communicate change to team: why version authority, what users see

### Phase 2: MCPs, Complexity, Governance (Weeks 3–4)

**Week 3:**
- [ ] Add `required_mcps`, `optional_mcps`, `complexity` fields to `agent.schema.json`
- [ ] Audit all 69 agents: fill in new fields, classify by complexity
- [ ] Update plugin load logic to check MCPs at runtime and emit warnings
- [ ] Document complexity tiers in CONTRIBUTING.md with examples

**Week 4:**
- [ ] Add deprecation fields to `agent.schema.json`: `deprecation_date`, `successor_agent`, `migration_guide_url`
- [ ] Write deprecation policy in CONTRIBUTING.md (6-week grace period, archive process)
- [ ] Set up SemVer validation in CI (fail if version bump doesn't match changelog)
- [ ] Create PR template for agent changes (checklist for reviewers)
- [ ] Run a deprecation dry-run: identify 1-2 agents to deprecate as proof-of-concept

---

## Risk & Benefit Analysis

### Decision 1: Version Authority

| Dimension | Risk | Benefit |
|-----------|------|---------|
| **Velocity** | CI adds ~30min to each agent release | Single source of truth eliminates debugging version conflicts |
| **Maintenance** | Initial setup cost (~8 hours) | Long-term savings: no manual syncing, fewer merge conflicts |
| **Discovery** | No immediate UX change | Enables faster agent discovery (users see latest metadata instantly) |
| **Onboarding** | Requires learning CI workflow | Clear expectations: one place to edit, one place to review |

### Decision 2: Router Simplification

| Dimension | Risk | Benefit |
|-----------|------|---------|
| **Discoverability** | Users must pull latest router to see new agents | Agents published immediately when merged to `.github/agents/` |
| **Maintenance** | CI failure blocks agent releases | One source of truth for marketplace (no duplication, no staleness) |
| **Complexity** | Removes thin-wrapper pattern (philosophical shift) | Simpler codebase: fewer files, fewer things to update |

### Decision 3: Required MCPs

| Dimension | Risk | Benefit |
|-----------|------|---------|
| **UX** | Users may discover missing MCPs at runtime (not install time) | Flexible: supports both mandatory and optional MCPs; graceful degradation |
| **Support** | Requires clear error messages (will need iteration) | Transparency: users understand agent dependencies upfront |
| **Validation** | No install-time validation (relies on runtime checks) | Fewer install blockers; users can configure MCPs asynchronously |

### Decision 4: Complexity Metadata

| Dimension | Risk | Benefit |
|-----------|------|---------|
| **Subjectivity** | Classification is subjective; may need adjustment over time | Reduces cognitive load for new users; self-selection improves satisfaction |
| **Maintenance** | New field to maintain (3 tiers × 69 agents) | Better discovery: marketplace can sort/filter by tier; fewer support tickets |
| **Scalability** | Simple tier system may not scale beyond 100+ agents | Foundation for future refinement (prerequisites, use-case tags) |

### Decision 5: Deprecation Policy

| Dimension | Risk | Benefit |
|-----------|------|---------|
| **User Impact** | 6-week grace period may not be enough for all users | Predictable sunset; migration guides reduce friction |
| **Maintenance** | Deprecated agents still consume resources (until archived) | No breaking changes: users have time to migrate; archival is clean |
| **Complexity** | Requires monitoring deprecation dates; archival workflow needed | Clear lifecycle for agents; customers feel supported, not abandoned |

### Decision 6: Change Authority

| Dimension | Risk | Benefit |
|-----------|------|---------|
| **Velocity** | PR review adds delay (~1 day per change) | Catches mistakes early; enforces SemVer discipline; clear accountability |
| **Scaling** | 69 agents → many PRs; review bandwidth may be strained | Distributed via CODEOWNERS (domain experts own their agents) |
| **Clarity** | SemVer discipline requires training | No more "why did version jump from 1.0 to 2.5?" surprises |

---

## Stakeholder Talking Points

1. **"Why auto-generate router stubs? Can't teams just edit them manually?"**  
   → Single source of truth prevents conflicts. Manual edits create drift. CI auto-sync is the industry standard (npm, VS Code, etc.).

2. **"What if an MCP is optional? Declaring it `required` might block users unnecessarily."**  
   → We distinguish `required_mcps` (mandatory) from `optional_mcps` (nice-to-have). Runtime validation is graceful: users see a warning but can still use the agent (with reduced functionality).

3. **"Is complexity tier really necessary? Can't users just read the description?"**  
   → Reading descriptions takes time. A tier enables self-selection and reduces support burden. For a growing agent library, this is essential UX.

4. **"Why 6 weeks for deprecation? That seems short."**  
   → 6 weeks is industry standard (npm, Python PyPI). It's long enough for most teams to migrate, short enough to keep codebase maintainable. We can extend to 8 weeks if needed based on feedback.

5. **"Who are the 'domain experts' in CODEOWNERS? How do we assign them?"**  
   → Start with agents' current maintainers. As agents are created, the author becomes the expert until they hand off. Clear in CONTRIBUTING.md.

6. **"What if we need to change an agent but the maintainer is unavailable?"**  
   → Fallback: `@engineering-team` can approve. But prefer reaching out to maintainer first (best practice).

---

## Next Steps

1. **Circulate this document to stakeholders** for async feedback (48-hour window)
2. **Schedule 30-min sync** to discuss any open questions
3. **Start Phase 1 implementation** (weeks 1–2) once consensus is reached
4. **Announce rollout plan** to LightSpeed teams and customers (be transparent about why and when)

---

**For questions or refinements, reach out to the engineering leadership team.**