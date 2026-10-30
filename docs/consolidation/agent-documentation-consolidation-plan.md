# Agent Documentation Consolidation Plan

**Date:** 2026-10-29  
**Audit Scope:** ls-claude-agents-router repository  
**Status:** Current State Analysis Complete

---

## Executive Summary

The ls-claude-agents-router repository uses a **two-tier agent documentation model**: thin wrapper specifications in `agents/*.agent.md` that reference comprehensive central definitions in `~/.github/agents/`. This creates significant duplication and maintenance overhead, with metadata scattered across 3-4 locations (router `.agent.md`, central `AGENT.md`, provider-specific `claude/agent.md`, and optional README files).

**Key Finding:** 69 agents are documented, but with 4-5 different metadata formats and inconsistent frontmatter schemas across AGENT.md files, creating governance and searchability problems.

---

## 1. Current State Matrix: Three Real Examples

### Example 1: Changelog Agent (Simple Agent)

**Location 1:** `ls-claude-agents-router/agents/changelog.agent.md`
```yaml
---
name: changelog
description: "Use this agent when the user needs a changelog created..."
tools: Read, Grep, Glob, Bash, Edit, Write
model: sonnet
---
```

**Location 2:** `~/.github/agents/changelog-agent/AGENT.md`
```yaml
---
name: "Changelog Agent"
description: "Portable changelog management agent with Keep a Changelog 1.1.0..."
file_type: "agent"
category: "release-management"
status: "active"
visibility: "public"
tags:
  - changelog
  - keep-a-changelog
  - validation
  - formatting
  - portable
  - release-management
version: "v1.0.0"
created_date: "2026-08-01"
last_updated: "2026-08-25"
author: "Ash Shaw"
maintainer: "Ash Shaw"
owners: ["lightspeedwp/maintainers"]
language: "en"
implementation: "agents/changelog-agent/"
permissions:
  - read
  - write
  - filesystem
---
```

**Location 3:** No `claude/agent.md` exists (single-format agent)

**Duplications Found:**
- `name` (different formatting: "changelog" vs "Changelog Agent")
- `description` (different levels of detail)
- `version` (implicit in router file, explicit in AGENT.md)
- `status` (only in AGENT.md, implied in router)

**Metadata Location:**
- Router only: `tools`, `model`
- AGENT.md only: `category`, `tags`, `permissions`, `implementation`, `author`, `created_date`, `last_updated`
- Nowhere: required MCPs, complexity level, team ownership

---

### Example 2: Linear Advisor Agent (Multi-Provider Agent)

**Location 1:** `ls-claude-agents-router/agents/linear-advisor.agent.md`
```yaml
---
name: linear-advisor
description: "Use this agent for Linear project-management advisory work..."
tools: Read, Grep, Glob, WebSearch
model: sonnet
---
```

**Location 2:** `~/.github/agents/linear-advisor-agent/AGENT.md`
```yaml
---
title: "AGENT"
description: "Project management integration tool for Linear issue management..."
name: Linear Advisor Agent
agent_id: agent-11
agent_slug: linear-advisor
agent_name: Linear Advisor Agent
domain: project-management
focus: linear-integration
version: 1.0.1
created_date: '2026-07-22'
maintainer: LightSpeed Team
license: GPL-3.0
stability: stable
status: active
providers:
  - claude
  - copilot
  - openai
capabilities:
  - linear-issue-management
  - project-planning
  - issue-workflow-automation
  - release-planning
  - sprint-management
  - team-coordination
tags:
  - project-management
  - linear
  - issue-tracking
  - sprint-planning
  - release-planning
  - workflow-automation
---
```

**Location 3:** `~/.github/agents/linear-advisor-agent/claude/agent.md`
```yaml
---
file_type: documentation
provider: claude
agent_slug: linear-advisor
agent_name: Linear Advisor Agent (Claude)
status: active
version: 1.0.2
created_date: '2026-07-22'
last_updated: '2026-08-21'
model_compatibility:
  - claude-opus-4
  - claude-sonnet-4
  - claude-haiku-4
context_window: '200000'
token_limit: '200000'
temperature: 0.7
top_p: 0.9
---
```

**Duplications Found:**
- `name`/`agent_name`: appears in 3 locations with variations ("linear-advisor", "Linear Advisor Agent")
- `description`: appears in 2 locations
- `version`: appears in 2 locations (1.0.1 vs 1.0.2 — **different values!**)
- `status`: appears in 2 locations
- `created_date`: appears in 2 locations

**Metadata Location:**
- Router only: `tools`
- AGENT.md only: `domain`, `focus`, `capabilities`, `providers`, `provider_config`, `license`, `stability`, `agent_id`
- Claude/agent.md only: `model_compatibility`, `context_window`, `temperature`, `top_p`
- Across both provider files: version mismatch (1.0.1 vs 1.0.2)

---

### Example 3: Design Partner Agent (Complex Multi-Provider)

**Location 1:** `ls-claude-agents-router/agents/design-partner.agent.md`
```yaml
---
name: design-partner
description: "Use this agent when the user needs design collaboration support..."
tools: Read, Grep, Glob, WebSearch
model: sonnet
---
```

**Location 2:** `~/.github/agents/design-partner-agent/AGENT.md`
```yaml
---
title: "AGENT"
description: "AI-powered design collaboration tool..."
name: Design Partner Agent
agent_id: agent-5
agent_slug: design-partner
agent_name: Design Partner Agent
agent_type: specialized
domain: design
focus: partner-collaboration
version: 1.0.1
created_date: '2026-07-22'
last_updated: '2026-08-21'
maintainer: LightSpeed Team
authors:
  - LightSpeed Team
license: GPL-3.0
stability: stable
status: active
tier: premium
capabilities:
  - design-consultation
  - design-system-management
  - ui-ux-review
  - accessibility-assessment
  - design-documentation
  - figma-integration
providers:
  - claude
  - copilot
  - openai
provider_config:
  claude:
    status: active
    tier: full
    tools: 8
    integrations:
      - figma-api
      - design-system-validator
      - accessibility-checker
  copilot:
    status: active
    tier: full
    skills: 6
    integrations:
      - github-sync
      - figma-sync
  openai:
    status: active
    tier: full
    functions: 8
    integrations:
      - figma-api
      - third-party-design-tools
tags:
  - design
  - collaboration
  - ui-ux
  - accessibility
  - figma
  - design-systems
---
```

**Location 3:** `~/.github/agents/design-partner-agent/claude/agent.md`
```yaml
---
title: "Agent"
provider: claude
agent_slug: design-partner
agent_name: Design Partner Agent (Claude)
status: active
version: 1.0.2
created_date: '2026-07-22'
last_updated: '2026-08-21'
model_compatibility:
  - claude-opus-4
  - claude-sonnet-4
  - claude-haiku-4
context_window: '200000'
token_limit: '200000'
temperature: 0.7
top_p: 0.9
---
```

**Duplications Found:**
- `agent_name`: 3 variations ("design-partner", "Design Partner Agent", "Design Partner Agent (Claude)")
- `status`: appears in 2 locations (both "active")
- `version`: appears in 2 locations (**different again: 1.0.1 vs 1.0.2**)
- `created_date`: appears in 2 locations

**Metadata Location:**
- Router only: `tools`
- AGENT.md only: `agent_id`, `agent_type`, `domain`, `focus`, `tier`, `capabilities`, `providers`, `provider_config`, `authors`, `license`, `stability`, `agent_slug`
- Claude/agent.md only: `model_compatibility`, `context_window`, `temperature`
- Scattered: naming conventions (slug vs name vs agent_name)

---

## 2. Duplications Found

| Metadata Field | Router | AGENT.md | claude/agent.md | Inconsistency Level |
|---|---|---|---|---|
| name/agent_name/agent_slug | ✓ | ✓ | ✓ | **HIGH** (3 formats) |
| description | ✓ | ✓ | ✗ | Medium (different detail) |
| version | ✗ | ✓ | ✓ | **HIGH** (values differ) |
| status | ✗ | ✓ | ✓ | Medium (always redundant) |
| created_date | ✗ | ✓ | ✓ | Medium (always redundant) |
| last_updated | ✗ | ✓ | ✓ | Medium (always redundant) |

**Total Duplication:** 6+ fields appear in 2-3 locations with different formats or values.

---

## 3. Information Gaps

### Missing Entirely from All Locations
- **Required MCPs** – No metadata about MCP dependencies
- **Complexity level** – No difficulty/expertise indicator
- **Expected model tier** – No guidance on which Claude model to use
- **Team ownership** – Who owns/maintains this agent? (exists in AGENT.md only for some)
- **Change management** – No deprecation path or migration guide field
- **Agent dependencies** – No way to express "this agent depends on X"
- **Usage metrics** – No invocation count, popularity, or adoption metadata

### Inconsistently Documented
- **Capabilities**: present in AGENT.md (structured array), but tools in router (comma-separated string) — no alignment
- **Model requirements**: router specifies "model: sonnet" (hardcoded), but claude/agent.md specifies `model_compatibility` (array) — which is authoritative?
- **Tools/integrations**: AGENT.md lists capabilities, Claude/agent.md lists tools, router lists tools — three different taxonomy systems
- **Category/Domain**: only in AGENT.md (field naming: `category` vs `domain` — used inconsistently)

---

## 4. Inconsistency Patterns

### Pattern 1: Name Field Variations
Across 69 agents, we find:
- `name: "changelog"` (slugified in router)
- `name: "Changelog Agent"` (title-cased in AGENT.md)
- `agent_name: "Changelog Agent (Claude)"` (with provider suffix in claude/agent.md)
- `agent_slug: "changelog"` (in AGENT.md)

**Impact:** No canonical identifier; requires manual normalization for searches/indexing.

### Pattern 2: Version Mismatch
- `linear-advisor`: 1.0.1 in AGENT.md, 1.0.2 in claude/agent.md
- `design-partner`: 1.0.1 in AGENT.md, 1.0.2 in claude/agent.md
- No clear authority on which is "source of truth"
- Suggests independent release cycles or lack of sync process

### Pattern 3: Different Frontmatter Schemas
- **Type 1** (Simple): name, description, tools, model only (e.g., `planner.agent.md`)
- **Type 2** (Full Documented): name, description, + rich metadata (changelog-agent/AGENT.md)
- **Type 3** (Versioned Multi-Provider): adds provider_config, agent_id, tier (design-partner, linear-advisor)
- **Type 4** (Legacy)**: Different field names (title vs name, focus vs domain)

No schema versioning or type hints to indicate which format is expected.

### Pattern 4: Tools/Capabilities Taxonomy Mismatch
- Router `.agent.md`: `tools: Read, Grep, Glob, WebSearch` (Claude SDK tools)
- AGENT.md: `capabilities: [design-consultation, ui-ux-review, accessibility-assessment, ...]` (business capabilities)
- claude/agent.md: Lists detailed tool specs with parameters

These are **three different taxonomies** describing the same thing at different levels of abstraction.

---

## 5. Proposed Single Source of Truth

### New Canonical Format: Consolidated Agent Specification

**Location:** `~/.github/agents/{agent-slug}/agent.json` (or AGENT.md with unified schema)

**Unified Frontmatter Schema:**

```yaml
---
# Identity (required)
agent_id: "agent-11"                    # Immutable, globally unique
agent_slug: "linear-advisor"            # Canonical kebab-case name for URLs, APIs
agent_name: "Linear Advisor Agent"      # Display name (title case)
agent_name_provider_suffix: "Linear Advisor Agent (Claude)"  # Provider-specific display

# Classification (required)
file_type: "agent"
category: "project-management"          # Business domain
focus: "linear-integration"             # Problem focus area
tier: "standard"                        # standard | premium | enterprise
agent_type: "specialized"               # specialized | general | mode | utility

# Status & Versioning (required)
status: "active"                        # active | deprecated | experimental | archived
version: "1.0.2"                        # Canonical single version (sync across all files)
created_date: "2026-07-22"
last_updated: "2026-08-21"
deprecation_date: null                  # null or ISO date if deprecated
migration_target: null                  # "new-agent-slug" if deprecated

# Ownership & Governance (required)
author: "LightSpeed Team"
maintainer: "LightSpeed Team"
owners: ["lightspeedwp/maintainers"]
license: "GPL-3.0"
visibility: "public"                    # public | private | internal
stability: "stable"                     # stable | beta | experimental

# Business Metadata (required)
description: "Project management integration tool for Linear..."
capabilities:
  - linear-issue-management
  - project-planning
  - issue-workflow-automation
tags:
  - project-management
  - linear
  - issue-tracking

# Provider Configuration (required)
providers:
  - claude
  - copilot
  - openai
provider_config:
  claude:
    status: "active"
    tier: "full"
    model_compatibility:
      - claude-opus-4
      - claude-sonnet-4
      - claude-haiku-4
    recommended_model: "claude-sonnet-4"
    tools_count: 8
    required_mcps:
      - linear-api
    integrations:
      - linear-api
      - issue-manager
    context_window: "200000"
    temperature: 0.7
    top_p: 0.9
  copilot:
    status: "active"
    tier: "full"
    skills_count: 6
  openai:
    status: "active"
    tier: "full"

# Dependencies & Requirements (required)
permissions:
  - read
  - write
implementation_folder: "agents/linear-advisor-agent/"
required_mcps: []                       # MCP server IDs required
depends_on_agents: []                   # Agent IDs this depends on
complexity: "intermediate"              # beginner | intermediate | advanced | expert

# Documentation (required)
readme_path: "README.md"
skill_references: []                    # References to SKILL.md files
---
```

### Rationale for Consolidation:

1. **Single Version Source** – Eliminates 1.0.1 vs 1.0.2 mismatches
2. **Clear Name Hierarchy** – agent_slug (API), agent_name (display), agent_name_provider_suffix (when needed)
3. **Provider Extensibility** – provider_config object scales to 10+ providers without schema drift
4. **Governance** – Explicit fields for deprecation, migration, complexity, and ownership
5. **Dependency Tracking** – Can express "this agent requires X MCP" and "this agent depends on Y agent"
6. **Searchability** – Tags, category, complexity as queryable fields
7. **Type Safety** – Clear enum values (status, tier, visibility, complexity) prevent inconsistency

---

## 6. Migration Strategy

### Phase 1: Create Unified Schema (Week 1)

1. **Define canonical schema** (above) in schema documentation
2. **Create JSON Schema validator** at `~/.github/agents/agent.schema.json`
3. **Add schema version** field to new frontmatter (e.g., `schema_version: "2.0"`)
4. **Document breaking changes** from v1 to v2

### Phase 2: Audit Current State (Week 2)

1. **Scan all 69 agents** for current metadata patterns
2. **Identify 3-5 sub-patterns** (vs the 4 we found)
3. **Create mapping table**: old field → new field for each pattern
4. **Flag agents with version mismatches** (e.g., linear-advisor 1.0.1 vs 1.0.2)
5. **Decide which version is authoritative** per agent

### Phase 3: Transform AGENT.md Files (Week 3)

1. **For each agent directory**, transform `AGENT.md` frontmatter:
   - Use mapping table from Phase 2
   - Merge data from `claude/agent.md` (model_compatibility, etc.) into provider_config
   - Use consistent naming (agent_slug, agent_name, agent_name_provider_suffix)
   - Resolve version conflicts (pick canonical version, timestamp both sources)

2. **Schema validation** – Validate each transformed AGENT.md against JSON Schema

3. **Backup originals** – Git commit with message "BACKUP: original AGENT.md before consolidation"

**Example Migration (linear-advisor):**

Before:
```yaml
# AGENT.md
name: Linear Advisor Agent
version: 1.0.1
created_date: '2026-07-22'

# claude/agent.md
version: 1.0.2
model_compatibility: [claude-opus-4, claude-sonnet-4, claude-haiku-4]
```

After:
```yaml
# AGENT.md (unified)
agent_name: Linear Advisor Agent
version: 1.0.2          # Pick higher version; document why
created_date: '2026-07-22'
provider_config:
  claude:
    model_compatibility: [claude-opus-4, claude-sonnet-4, claude-haiku-4]
    version_note: "Upgraded from 1.0.1 to 1.0.2 during consolidation (2026-10-29)"
```

### Phase 4: Simplify Router .agent.md Files (Week 4)

1. **Reduce router files** to minimal stub format:
   ```yaml
   ---
   agent_slug: "linear-advisor"
   # Fetch full spec from ~/.github/agents/{agent_slug}/AGENT.md
   ---
   
   This agent is defined in ~/.github/agents/linear-advisor-agent/AGENT.md
   ```

2. **Or eliminate router files entirely** – Auto-generate them from AGENT.md

3. **Update plugin.json** to reference AGENT.md directly if possible

### Phase 5: Establish Sync Process (Week 4-5)

1. **Create pre-commit hook** – Validates AGENT.md against schema before commit
2. **Create GitHub Action** – On PR to main, validate schema of modified AGENT.md files
3. **Create sync script** – Auto-update router files from ~/.github/agents/ if needed
4. **Document ownership** – Who updates version when? Central registry or distributed?

### Phase 6: Verification & Documentation (Week 5)

1. **Audit script** – Count agents by status, category, provider
2. **Dependency graph** – Validate no circular agent dependencies
3. **Coverage report** – All 69 agents meet new schema
4. **README updates** – Document new schema, migration narrative
5. **Rollout communication** – Announce to team

---

## 7. Example Consolidated Spec: "Linear Advisor Agent"

### Current State (Scattered Across 3 Files)

**File 1:** `ls-claude-agents-router/agents/linear-advisor.agent.md`
```yaml
name: linear-advisor
description: "Use this agent for Linear project-management advisory work..."
tools: Read, Grep, Glob, WebSearch
model: sonnet
```

**File 2:** `~/.github/agents/linear-advisor-agent/AGENT.md`
```yaml
name: Linear Advisor Agent
agent_id: agent-11
version: 1.0.1
created_date: '2026-07-22'
domain: project-management
focus: linear-integration
capabilities: [linear-issue-management, project-planning, ...]
providers: [claude, copilot, openai]
```

**File 3:** `~/.github/agents/linear-advisor-agent/claude/agent.md`
```yaml
version: 1.0.2
model_compatibility: [claude-opus-4, claude-sonnet-4, claude-haiku-4]
context_window: '200000'
temperature: 0.7
```

### Consolidated Spec (Single AGENT.md)

```yaml
---
# Identity
agent_id: "agent-11"
agent_slug: "linear-advisor"
agent_name: "Linear Advisor Agent"

# Classification
file_type: "agent"
category: "project-management"
focus: "linear-integration"
tier: "standard"
agent_type: "specialized"

# Status
status: "active"
version: "1.0.2"
created_date: "2026-07-22"
last_updated: "2026-08-21"

# Ownership
author: "LightSpeed Team"
maintainer: "LightSpeed Team"
owners: ["lightspeedwp/maintainers"]
license: "GPL-3.0"
visibility: "public"
stability: "stable"

# Business
description: "Project management integration tool for Linear issue management, sprint planning, and team coordination"
capabilities:
  - linear-issue-management
  - project-planning
  - issue-workflow-automation
  - release-planning
  - sprint-management
  - team-coordination
tags:
  - project-management
  - linear
  - issue-tracking
  - sprint-planning
  - release-planning

# Provider Config
providers:
  - claude
  - copilot
  - openai
provider_config:
  claude:
    status: "active"
    tier: "full"
    recommended_model: "claude-sonnet-4"
    model_compatibility:
      - claude-opus-4
      - claude-sonnet-4
      - claude-haiku-4
    tools_count: 6
    tools:
      - linear-api-client
      - issue-manager
      - project-planner
      - sprint-organizer
      - release-planner
      - workflow-automator
    required_mcps: []
    integrations:
      - linear-api
      - harvest
      - figma
    context_window: "200000"
    token_limit: "200000"
    temperature: 0.7
    top_p: 0.9
  copilot:
    status: "active"
    tier: "full"
    skills_count: 6
  openai:
    status: "active"
    tier: "full"

# Dependencies
permissions:
  - read
  - write
required_mcps: []
depends_on_agents: []
complexity: "intermediate"
implementation_folder: "agents/linear-advisor-agent/"
readme_path: "README.md"
---

# Linear Advisor Agent

[Rest of content as before]
```

**Benefits:**
- ✅ Single version source (1.0.2, not 1.0.1 vs 1.0.2)
- ✅ Canonical naming (agent_slug: "linear-advisor", not scattered variations)
- ✅ Complete provider config in one place
- ✅ No duplication across files
- ✅ Searchable fields: category, complexity, capabilities, tags
- ✅ Clear deprecation path if needed

---

## 8. Implementation Checklist

### Pre-Migration
- [ ] Create `agent.schema.json` with unified schema
- [ ] Audit current AGENT.md files, identify all variants
- [ ] Create mapping table (old field → new field)
- [ ] Get team approval on new schema
- [ ] Create branch: `consolidate/agent-docs`

### Migration
- [ ] Write consolidation script (validate + transform)
- [ ] Test on 3 sample agents (changelog, linear-advisor, design-partner)
- [ ] Transform all 69 AGENT.md files
- [ ] Validate all transformed files against schema
- [ ] Update router `.agent.md` to reference consolidated specs
- [ ] Create git commit with migration details

### Verification
- [ ] Run audit script: count agents by status, category
- [ ] Verify no version mismatches remain
- [ ] Verify all agents have canonical naming
- [ ] Spot-check 5 random agents for completeness

### Deployment
- [ ] Create PR with detailed migration narrative
- [ ] Get code review + team approval
- [ ] Merge to main
- [ ] Tag release version (e.g., v2.0.0)

### Post-Migration
- [ ] Set up GitHub Action for schema validation on new PRs
- [ ] Document new process for adding/updating agents
- [ ] Update README with schema reference
- [ ] Train team on new format

---

## 9. Success Criteria

| Criterion | Current State | Target State | Impact |
|---|---|---|---|
| Version consistency | 1.0.1 vs 1.0.2 mismatches exist | Single canonical version | Eliminates confusion; enables accurate tracking |
| Naming normalization | 3+ formats per agent | 1 canonical slug + name | Enables API-friendly searches and tooling |
| Duplication | 6+ fields in 2-3 locations | 1 authoritative location | 50% reduction in maintenance burden |
| Schema consistency | 4+ variants across AGENT.md files | 1 enforced schema (JSON Schema) | Enables automation, linting, validation |
| Metadata coverage | 8/15 critical fields present | 15/15 critical fields present | Better governance and discoverability |
| Dependency tracking | Not possible | Queryable agent dependencies | Enables impact analysis for changes |
| Deprecation paths | Informal, undocumented | Explicit deprecation_date + migration_target | Enables safe agent lifecycle management |

---

## 10. Risks & Mitigation

| Risk | Mitigation |
|---|---|
| Breaking changes for tool consumers | Deprecate gradually; support both v1 and v2 schema for 2 weeks |
| Version conflicts cannot be resolved automatically | Create manual decision log per agent; document rationale in git commit |
| Some AGENT.md files deeply nested (e.g., with SKILL.md references) | Preserve structure; only consolidate frontmatter, leave body content untouched |
| Plugin marketplace caches old specs | Clear cache after merge; document in CHANGELOG |
| Team resistance to schema changes | Get buy-in early; show efficiency gains (less duplication to maintain) |

---

## 11. Open Questions for Stakeholder Discussion

1. **Version Authority:** When AGENT.md and claude/agent.md have different versions (1.0.1 vs 1.0.2), which is authoritative? Should they always be in sync?

2. **Router Simplification:** Should `ls-claude-agents-router/agents/*.agent.md` be eliminated entirely and auto-generated from `~/.github/agents/AGENT.md`, or kept as stubs?

3. **Required MCPs:** Should agents declare required MCPs (e.g., `required_mcps: [linear-api]`)? If so, should the plugin validate at install time?

4. **Complexity Metadata:** Should we add a `complexity: beginner|intermediate|advanced` field to help users choose the right agent?

5. **Deprecation Policy:** When deprecating an agent, what's the handoff process? (e.g., if `linear-advisor` is deprecated in favor of a new `linear-v2-advisor`, should we auto-redirect requests?)

6. **Change Authority:** Who can update AGENT.md? Should changes require review? Should version bumps follow SemVer?

---

## References

- **Router Repository:** `/Users/ash/.claude/plugins/marketplaces/lightspeedwp/ls-claude-agents-router/`
- **Central Agents:** `/Users/ash/.github/agents/`
- **Examples Audited:**
  - `changelog-agent` (simple)
  - `linear-advisor-agent` (multi-provider with version mismatch)
  - `design-partner-agent` (complex with rich provider_config)

---

**Consolidation Plan Document**  
Generated: 2026-10-29  
Status: Ready for Stakeholder Review
