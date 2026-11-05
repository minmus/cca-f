# Does Claude keep the context window between sessions?

**Stage 1** of the [learning path](../study-map/learning-path.md) · **D5 › 5.1 Context Window Management** · **D1 › 1.7 Session State & Resumption**
**Goes with:** [Q46](../questions/Q46-stateless-api-pass-full-history/question.md) (the API is stateless) · [Q45](../questions/Q45-fresh-session-structured-summary/question.md), [Q72](../questions/Q72-resume-and-name-changed-files/question.md), [Q92](../questions/Q92-resume-named-session-by-name/question.md) (resume or start fresh)

## The question

> Does Claude store your context windows across sessions? Does opening a session later also retrieve its context window?

## Short answer

**The model stores nothing.** A context window isn't a thing that gets saved. It's simply **everything sent in one request**. What gets saved is the **message history**, and saving it is the job of whatever program sits around the model.

When you resume, that program **re-sends the saved history**, and the model builds a fresh context window from it on that request.

## The key idea: context window = one request

Guide 5.1: *"The context window is everything Claude reads on one request."* *"The Claude API is stateless."* *"There is no server-side session state, so each request must include the complete conversation history."*

Think of the model as someone with **no memory at all**. Before every reply, someone hands them the **whole conversation so far** on paper. They read it, answer, and forget everything. The paper is the context window, and somebody else keeps it.

## Who keeps the "paper"? Three levels

| Level | Who stores the history | What "resume" does |
|---|---|---|
| **Claude API** (your own code) | **Nobody**, unless your code saves it (database, file) | Your code loads the saved messages and sends them again (Q46) |
| **Claude Code / Agent SDK** | **Claude Code**, as a transcript file on your disk | `--continue` / `--resume` reads the transcript and **re-sends** it |
| **claude.ai chat** | claude.ai stores the conversation | Reopening a chat sends its history again with your next message |

**In Claude Code**, sessions are saved as JSONL at `~/.claude/projects/<project>/<session-id>.jsonl` (docs). This study session's own transcript, checked 2026-11-05:

```text
$ ls ~/.claude/projects/c--Users-mindaugas-musnikas-d-Documents-CODE-CLAUDE-CCA-F/*.jsonl
    13.6 MB  Nov 5 10:21  29debd0b-b6d4-4000-b31a-fdfadda5ae52.jsonl
```

That 13.6 MB file is the "paper": every message, tool call and tool result. It **isn't** a saved context window. Much of it won't fit in one, which is why long sessions get **compacted**.

## What a resume brings back, and what it doesn't

From the Claude Code docs, *"What a resumed session restores"*:

| Restored | Not restored / not the same |
|---|---|
| **Conversation history**: *"the messages, tool calls, and results"* | **The world outside**: files changed since then. Old tool results are **stale** (Q45, Q72: tell Claude what changed) |
| If compacted: *"the last compaction's summary"* + messages since | The messages that compaction summarised: *"aren't loaded again"*. Only the summary comes back |
| The model, the agent, the permission mode (mostly) | A tool call that was running when it crashed: it *"doesn't get rerun"* |
| | Some launch flags (`--mcp-config`, `--add-dir`…) must be passed again |

CLAUDE.md isn't part of the transcript. It's **re-read from disk** at the start of every session, new or resumed, so you always get its **current** version.

## Two things that look like memory, but aren't the context window

- **CLAUDE.md and auto memory:** files Claude Code **loads into every new session**. That's persistent *instructions/notes*, not the old conversation.
- **Prompt caching:** the API can cache a repeated prompt prefix for a few minutes so re-sending it is cheaper and faster. It saves **cost**, not **memory**: you still send the full history.

> **The model forgets everything after each request. "Memory" is always a program re-sending saved text: your code (API), a transcript file (Claude Code), or files loaded at startup (CLAUDE.md, auto memory).**

## Check question

**Yesterday, in a Claude Code session, Claude read `auth.py`. Overnight a teammate changed `auth.py`. Today you resume the session with `claude --continue`. (1) Does Claude "know" about the change? Why or why not? (2) What should you do about it?**

*(Your answer and the feedback will be recorded here.)*

## Sources

- Guide D5 › 5.1 Context Window Management (checked 2026-11-05): *"The context window is everything Claude reads on one request"*; *"The Claude API is stateless."*; *"There is no server-side session state, so each request must include the complete conversation history."*
- Claude Code docs, [Manage sessions](https://code.claude.com/docs/en/sessions) (checked 2026-11-05): *"A session is a saved conversation tied to a project directory. Claude Code stores it locally as you work"*; *"What a resumed session restores"*; transcripts at `~/.claude/projects/<project>/<session-id>.jsonl`; 30-day default retention (`cleanupPeriodDays`).
