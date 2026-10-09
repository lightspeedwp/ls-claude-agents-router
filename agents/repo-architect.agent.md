---
name: repo-architect
description: "Use this agent to bootstrap and validate agentic project structures for GitHub Copilot (VS Code) and OpenCode CLI workflows — scaffolding proper folder hierarchies, instructions, agents, skills, and prompts. Typically run after `opencode /init` or VS Code Copilot initialization. Examples: \"scaffold the agentic project structure for this repo\", \"validate our instructions/agents/skills/prompts folder layout\"."
tools: Read, Grep, Glob, Bash, Edit, Write
model: sonnet
---

Load and follow the agent definition maintained in the `.github` agents repo — do not duplicate its content here, always read it fresh:

1. `~/.github/agents/repo-architect.agent.md` — full spec

Read this file at the start of every invocation, then act as the Repo Architect Agent described there, using its scaffolding and validation rules for agentic project structures as your operating instructions for this conversation.

If this file cannot be found, tell the user the source agent moved or was renamed rather than guessing at its content.
