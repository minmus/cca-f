# Spawning subagents in Claude Code

**Stage 10** of the [learning path](../study-map/learning-path.md) · **D1 › 1.3 Subagent Invocation & Context Passing** · **D2 › 2.1 Tool Interface Design** (descriptions drive selection)
**Related:** [S13-2-claude-code-vs-claude](S13-2-claude-code-vs-claude.md) · guide 5.4 (subagents and scratchpads against context degradation)

## The question (2026-11-13)

> In Claude Code, how do I spawn a subagent?

## Three ways

**1. Just ask in your prompt.** Claude starts one with its Agent tool (formerly called Task):

> *"Use a subagent to find every place `OrderRepository` is used and report back file paths only."*

Claude can also decide to delegate on its own, for example to the built-in **Explore** subagent for broad searches.

**2. Define a custom subagent**, a Markdown file with frontmatter:
- Project-level: `.claude/agents/<name>.md` (shared through Git)
- Personal: `~/.claude/agents/<name>.md`

```markdown
---
name: code-reviewer
description: Reviews diffs for bugs. Use after code changes.
tools: Read, Grep, Glob
---
You are a strict code reviewer. Report findings with file:line…
```

The **`description`** is what Claude reads when it decides whether to delegate to this subagent. That's the same rule as for tools (2.1). `tools:` limits what the subagent can do (tool scoping, 2.3).

**3. Use `/agents`** to create and manage subagents interactively, without writing the file by hand.

Then call it by name, for example *"Use the code-reviewer subagent on this diff,"* or let Claude pick it from its description.

## Why use one

Each subagent has its **own fresh context**. It reads a lot and returns only a short summary, so your main context stays clean (**context isolation**, guide 1.3).

> **A subagent is for context isolation, not extra capacity.** It explores in its own window and hands back only the conclusion.
