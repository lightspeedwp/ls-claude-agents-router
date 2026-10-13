# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added

- Added the `wp-db-migration` agent, which wraps
  `agents/wp-db-migration-agent/` from `lightspeedwp/.github` (spec 020). It
  makes MySQL exports importable into WordPress Studio, checks Studio exports
  before staging or live, and replaces site URLs safely.

## [1.0.4] - 2026-10-09

### Changed

- Test release used to verify that plugin updates reach installed copies
  through the self-hosted `lightspeedwp` marketplace. It contains no
  functional changes beyond those listed below.

### Added

- Added the `repository` field to `.claude-plugin/plugin.json`, pointing at
  `https://github.com/lightspeedwp/ls-claude-agents-router`.
- Added `.claude-plugin/marketplace.json` so the repository works as its own
  `lightspeedwp` marketplace, and install and update steps to the README.

### Fixed

- Quoted every agent's `description` in its frontmatter. Each one contained an
  unquoted `: ` (for example `Examples: "..."`), which made the YAML invalid,
  so all 67 agents loaded without their name, description, tools, or model.
  `claude plugin validate .` now passes.
- Pointed agents at `AGENT.md` as their primary source wherever
  `lightspeedwp/.github` has one, matching the new standard there. 36 agents
  now read `AGENT.md` first. Spec files that duplicated it were dropped,
  `claude/agent.md` stays second, and specs with distinct content
  (`task-planner`, `task-researcher`, `prd-factory-planner`,
  `client-website-discovery`) are kept as supplementary reads.
- Removed duplicated `1.0.1`–`1.0.3` sections from this changelog and moved
  the marketplace entries back under `[Unreleased]`.

## [1.0.3] - 2026-10-09

### Changed

- Renamed the plugin from `lsx-agents` to `ls-claude-agents-router` and
  updated its description. Agents are now invoked with the
  `ls-claude-agents-router:` prefix instead of `lsx-agents:`, so any prompts,
  docs, or settings using the old prefix need updating.
- Switched `$schema` in `.claude-plugin/plugin.json` to the community
  `claude-code-json-schema` plugin schema.

### Fixed

- Pointed `$schema` at the raw JSON file so editors can resolve it; it
  previously linked to a GitHub HTML page.
- Removed an accidentally committed `.DS_Store` file and added a `.gitignore`
  to keep it out.

## [1.0.2] - 2026-09-30

### Fixed

- Repointed `pr-creation` at `pr-agent/AGENT.md`. The
  `pr-creation-agent/pr-creation.agent.md` spec it referenced was deleted from
  `lightspeedwp/.github` when that folder was merged into `pr-agent/`, so the
  agent could not load its instructions.

## [1.0.1] - 2026-09-18

### Added

- Added 29 new agents that existed in `lightspeedwp/.github` `agents/` but
  weren't yet represented in this plugin: `address-comments`,
  `agent-governance-reviewer`, `ai-readiness-reporter`, `ai-team-dev`,
  `ai-team-producer`, `ai-team-qa`, `blueprint-mode`,
  `frontend-performance-investigator`, `github-actions-expert`,
  `github-actions-node-upgrade`, `linkedin-post-writer`,
  `markdown-accessibility-assistant`, `meta-agentic-project-scaffold`,
  `modernization`, `php-mcp-expert`, `plan`, `planner`, `playwright-tester`,
  `qa-subagent`, `quality-playbook`, `refine-issue`, `repo-architect`,
  `se-gitops-ci-specialist`, `specification`, `tdd-red`, `tdd-green`,
  `tdd-refactor`, `tech-debt-remediation-plan`, and `thinking-beast-mode`.
  Total agent count is now 67.

### Fixed

- Updated all 34 agent files to point to the correct source locations in the
  `lightspeedwp/.github` `agents/` directory after its restructuring moved
  spec files from `agents/{name}.agent.md` into `agents/{name}-agent/{name}.agent.md`.
- Renamed reference for `mode-document-reviewer.agent.md`, whose source spec
  was renamed to `document-reviewer-agent/document-reviewer.agent.md`.
- Redirected implementation references for `ai-readiness-estimator`,
  `client-website-discovery-assistant`, `website-content-strategist`,
  `website-scope-estimator`, `pr-creation`, and `harvest-analytical` to the
  `.github` folders that currently hold their real `AGENT.md`/`claude/agent.md`
  implementations (`ai-readiness-agent/`, `discovery-agent/`,
  `content-strategy-agent/`, `estimator-agent/`, `pr-agent/`, and
  `archived/harvest-analytical-agent/` respectively), since the newer
  `.github` folders matching those spec names currently hold only the spec
  file.
