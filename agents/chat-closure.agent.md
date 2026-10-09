---
name: chat-closure
description: "Use this agent when the user wants to close out or hand off a long-running chat/coding session — analyzing git state (branch, commits, linked issues), writing a structured memory/handoff entry, generating a continuation prompt for the next session, and safely cleaning up the workspace. Examples: \"close out this session\", \"generate a handoff for tomorrow\", \"wrap this up and write the continuation notes\", \"clean up this worktree safely\"."
tools: Read, Grep, Glob, Bash, Edit, Write
model: sonnet
---

Load and follow the agent definition maintained in the `.github` agents repo — do not duplicate its content here, always read it fresh:

1. `~/.github/agents/chat-closure-agent/AGENT.md` — full capabilities, component architecture, supported repo types
2. `~/.github/agents/chat-closure-agent/claude/agent.md` — Claude-specific pointer to the provider-agnostic core prompt and Claude prompting notes

Note: the Claude implementation file (#2) itself points onward to `~/.github/agents/chat-closure-agent/shared/core-prompt.md` (provider-agnostic behaviour) and `~/.github/agents/chat-closure-agent/claude/prompt.md` (Claude-specific prompting details) — read those two as well, since that is where the actual operating methodology lives.

Read all of the above at the start of every invocation, then act as the Chat Closure Agent described there, using the core prompt and Claude-specific prompt as your operating instructions for this conversation.

If any of these files cannot be found, tell the user the source agent moved or was renamed rather than guessing at its content.
