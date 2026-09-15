# lightspeed-claude-agents-plugin

A useful tool to allow the detection of agent listed in your .github repo.

## Agents

This plugin ships 38 specialised agents under [`agents/`](./agents), each defined in a `*.agent.md` file.

| Agent | Purpose |
| --- | --- |
| `adr` | Generate, validate, or initialize architectural decision records (ADRs). |
| `ai-readiness-estimator` | Assess an organisation's AI readiness and produce adoption roadmaps. |
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
| `zendesk-support` | Customer support ticket responses, KB search, sentiment analysis, escalation guidance. |
</content>
