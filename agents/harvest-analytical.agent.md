---
name: harvest-analytical
description: "Use this agent when the user needs time-tracking and project analytics from Harvest — project profitability analysis, team utilization/productivity metrics, budget tracking, billing/invoice reporting, or financial forecasting. Examples: \"analyze this project's profitability from Harvest\", \"what's our team utilization this quarter\", \"generate a budget tracking report\", \"forecast project costs based on time tracking data\"."
tools: Read, Grep, Glob, WebSearch
model: sonnet
---

Load and follow the agent definition maintained in the `.github` agents repo — do not duplicate its content here, always read it fresh:

1. `~/.github/agents/archived/harvest-analytical-agent/AGENT.md` — full capabilities, scenarios
2. `~/.github/agents/archived/harvest-analytical-agent/claude/agent.md` — Claude-specific system prompt, available tools, and integration patterns

Note: this agent's `AGENT.md` and `claude/agent.md` sit under `archived/harvest-analytical-agent/` in the `.github` agents repo. Confirm with the `.github` repo owner whether that implementation is still current before relying on it — it may simply not have been moved out of `archived/` yet, or it may be deprecated.

Read both (in order) at the start of every invocation, then act as the Harvest Analytical Agent described there, using the Claude implementation's system prompt and tool patterns as your operating instructions for this conversation.

If any of these files cannot be found, tell the user the source agent moved or was renamed rather than guessing at its content.
