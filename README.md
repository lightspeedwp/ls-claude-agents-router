# ls-claude-agents-router

A useful tool to allow the detection of agent listed in your .github repo.

## Installation

This repository is its own plugin marketplace (`.claude-plugin/marketplace.json`).
It's private, so your machine needs GitHub access stored for `git` first, for
example by running `gh auth login` and then `gh auth setup-git`.

In a Claude Code session, run:

```text
/plugin marketplace add lightspeedwp/ls-claude-agents-router
/plugin install ls-claude-agents-router@lightspeedwp
```

### Updates

You receive a new copy only when `version` in `.claude-plugin/plugin.json`
changes, so bump it with every release. To pull updates:

- Run `/plugin marketplace update lightspeedwp`, or
- Turn on auto-update under `/plugin` → **Marketplaces** → `lightspeedwp` →
  **Enable auto-update**.

## Agents

This plugin ships 69 specialised agents under [`agents/`](./agents), each defined in a `*.agent.md` file.

### LightSpeed business agents

| Agent | Purpose |
| --- | --- |
| `adr` | Generate, validate, or initialize architectural decision records (ADRs). |
| `ai-readiness-estimator` | Assess an organisation's AI readiness and produce adoption roadmaps. |
| `ai-readiness-reporter` | Run the AgentRC readiness assessment and generate a static HTML dashboard. |
| `changelog` | Create, update, and validate changelogs per Keep a Changelog 1.1.0. |
| `chat-closure` | Close out a coding session — handoff notes, continuation prompts, workspace cleanup. |
| `client-website-discovery` | Audit website architecture, UX, performance, and compliance for project scoping. |
| `client-website-discovery-assistant` | Assess a client's site (UX, SEO, performance) and benchmark against competitors. |
| `design-partner` | UI/UX and Figma design review, token extraction, WCAG 2.2 accessibility audits. |
| `harvest-analytical` | Time-tracking analytics from Harvest — profitability, utilisation, billing, forecasting. |
| `issues` | GitHub issue triage — classify type, set status/priority, enrich with acceptance criteria. |
| `labeling` | Apply and standardise GitHub labels against canonical `labels.yml`/`issue-types.yml`. |
| `linear-advisor` | Linear sprint planning, capacity/velocity analysis, release coordination. |
| `linting` | Validate/enforce code-quality standards across JS/TS, CSS, PHP, Python, Shell, etc. |
| `meta` | Front matter validation, badge blocks, and README/metadata maintenance for docs. |
| `metadata` | Audit metadata consistency and sync labels/PRs/project fields with confidence scoring. |
| `metrics` | Collect and report repository health metrics — issue/PR activity, response times. |
| `mode-demonstrate-understanding` | Socratic questioning to confirm the user understands code before proceeding. |
| `mode-document-reviewer` | Editorial review of technical docs/training content for accuracy and pedagogy. |
| `mode-thinking` | Autonomous, research-first problem-solving for complex or ambiguous coding tasks. |
| `pagespeed` | Core Web Vitals and performance analysis with optimisation recommendations. |
| `pr-creation` | Automate PR creation — branch validation, template selection, description formatting. |
| `prd` | End-to-end product planning — PRDs, user stories, roadmaps, sprint plans. |
| `prd-factory-planner` | Turn a product concept into a PRD plus implementation roadmap and resourcing plan. |
| `project-meta-sync` | Sync GitHub Project board fields (Status/Priority/Type) from labels and branch names. |
| `prompt-engineer` | Analyse, critique, and rewrite prompts and agent instructions. |
| `proposal-desk` | Draft client proposals, quotes, scopes of work, and related invoices. |
| `refine-issue` | Refine a requirement/issue with acceptance criteria, technical considerations, edge cases, NFRs. |
| `release` | Release management — readiness checks, version bumps, release PRs, notes. |
| `reporting` | Create and validate reports/progress logs per LightSpeed documentation conventions. |
| `reviewer` | Automated PR review — CI status, changed-file analysis, changelog checks, merge recommendation. |
| `task-planner` | Turn research into an executable task plan with dependencies and estimates. |
| `task-researcher` | Research requirements, constraints, and stakeholders before task planning. |
| `testing` | Generate test cases and automated test specs (Jest, PHPUnit, pytest, Playwright); run/triage suites. |
| `tour-operator-config` | Configuration guidance for WordPress/WooCommerce tour operator booking sites. |
| `website-content-strategist` | Content audits, gap analysis, keyword research, and content calendars. |
| `website-scope-estimator` | Scope and estimate website/software projects — features, effort, timeline, budget. |
| `woo-config` | WooCommerce store configuration and optimisation — payments, performance, catalogue. |
| `wordpress` | WordPress plugin/theme release versioning — SemVer validation and version bumps. |
| `wp-config` | WordPress site configuration — plugins, theming, performance, security hardening. |
| `wp-db-migration` | Move WordPress databases between MySQL live/staging sites and WordPress Studio — clean exports, pre-flight checks, safe URL replacement. |
| `wp-schema-audit` | Audit a WordPress site's JSON-LD structured data against schema.org and Google rich-result rules — test URLs, validation, prioritised report and CSV. |
| `zendesk-support` | Customer support ticket responses, KB search, sentiment analysis, escalation guidance. |

### General dev-persona / mode agents (Copilot-chatmode style specs)

| Agent | Purpose |
| --- | --- |
| `address-comments` | Address PR review comments — apply the fix and reply/resolve the thread. |
| `agent-governance-reviewer` | Review AI agent systems for governance, safety, policy enforcement, and audit trails. |
| `ai-team-dev` | AI development team persona (Nova/Sage/Milo) — implement features, fix bugs, write tests. |
| `ai-team-producer` | AI team producer persona (Remy) — plan work, coordinate dev/QA, never writes app code. |
| `ai-team-qa` | AI QA persona (Ivy) — test behaviour, file reproducible bugs, verify fixes. |
| `blueprint-mode` | Structured Debug/Express/Main/Loop coding workflows with strict correctness rules. |
| `frontend-performance-investigator` | Diagnose Core Web Vitals, Lighthouse regressions, layout shifts, long tasks. |
| `github-actions-expert` | Secure CI/CD workflows — action pinning, OIDC, least-privilege permissions. |
| `github-actions-node-upgrade` | Upgrade a GitHub Action's Node runtime with CI updates and validation. |
| `linkedin-post-writer` | Draft engagement-optimised LinkedIn posts from raw content or ideas. |
| `markdown-accessibility-assistant` | Improve markdown accessibility using GitHub best practices. |
| `meta-agentic-project-scaffold` | Scaffold agentic project workflows from the `github/awesome-copilot` collection. |
| `modernization` | Human-in-the-loop analysis, documentation, and planning for project modernisation. |
| `php-mcp-expert` | PHP MCP server development with the official PHP SDK and attribute-based discovery. |
| `plan` | Strategic planning/architecture analysis before implementation. |
| `planner` | Generate an implementation plan for new features or refactors. |
| `playwright-tester` | Write, run, and debug Playwright tests. |
| `qa-subagent` | Meticulous QA — test planning, bug hunting, edge-case analysis, verification. |
| `quality-playbook` | Six-phase (explore/generate/review/audit/reconcile/verify) quality engineering audit. |
| `repo-architect` | Scaffold and validate agentic project structures for Copilot/OpenCode workflows. |
| `se-gitops-ci-specialist` | CI/CD pipelines, deployment debugging, and GitOps reliability work. |
| `specification` | Generate or update specification documents for new or existing functionality. |
| `tdd-red` | TDD phase 1 — write failing tests from GitHub issue context before implementation. |
| `tdd-green` | TDD phase 2 — write minimal code to make failing tests pass. |
| `tdd-refactor` | TDD phase 3 — improve quality/security while keeping tests green. |
| `tech-debt-remediation-plan` | Generate technical debt remediation plans — analysis only, no code changes. |
| `thinking-beast-mode` | High-effort autonomous coding agent that keeps going until fully resolved. |

> **Note:** the general dev-persona/mode agents above are written in a VS Code
> Copilot chatmode-style spec format (Copilot tool names, varying `model` values)
> rather than the LightSpeed business-agent style. The plugin wraps each as a
> thin Claude stub that reads the source spec fresh from `.github/agents/` and
> follows its instructions, same as every other agent in this table.
</content>
