# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

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
