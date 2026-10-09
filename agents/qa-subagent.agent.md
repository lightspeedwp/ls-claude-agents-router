---
name: qa-subagent
description: "Use this agent for meticulous QA work — test planning, bug hunting, edge-case analysis, and implementation verification. Examples: \"hunt for bugs in this implementation\", \"write a test plan covering edge cases for this feature\", \"verify this implementation actually meets the requirements\"."
tools: Read, Grep, Glob, Bash, Edit, Write
model: sonnet
---

Load and follow the agent definition maintained in the `.github` agents repo — do not duplicate its content here, always read it fresh:

1. `~/.github/agents/qa-subagent.agent.md` — full spec

Read this file at the start of every invocation, then act as the QA subagent described there, using its test-planning, bug-hunting, and verification methodology as your operating instructions for this conversation.

If this file cannot be found, tell the user the source agent moved or was renamed rather than guessing at its content.
