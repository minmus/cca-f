# `/init` and `/memory`: who writes Claude Code's memory files

**Stage 13** of the [learning path](../study-map/learning-path.md) (CBT 1. Set Up Claude Code and Configure Projects) · **D3 › 3.1 CLAUDE.md Hierarchy**
**Builds on:** [S01-2-context-window-across-sessions](S01-2-context-window-across-sessions.md) (CLAUDE.md and auto memory load into every session) · [S01-4-external-memory](S01-4-external-memory.md) (storage outside the context, reached through tools)
**Goes with:** [Q56](../questions/Q56-memory-check-loaded-files-first/question.md) (`/memory` on the exam)

## The questions (2026-11-07)

> They're talking about `claude init`. Explain simply what it does. · Does it write to CLAUDE.md automatically or ask for approval? · What is the point of `/memory`? · Do you use any `/memory` in this project? · So `/memory` is the various CLAUDE.md files? · Are the .md files you produced used as some kind of memory?

## `/init`: Claude writes a first draft of CLAUDE.md

It's a command you type inside Claude Code. Claude reads your codebase and writes a starter CLAUDE.md.

- What goes in: *"build commands, test instructions, and project conventions it discovers."*
- If a CLAUDE.md already exists: *"`/init` suggests improvements rather than overwriting it."*
- After that, you edit it: *"Refine from there with instructions Claude wouldn't discover on its own."*

It's **only a starter generator**. The result is an ordinary CLAUDE.md, and Claude treats it as *"context, not enforced configuration."*

> **`/init` = Claude writes the first draft of CLAUDE.md. You refine it.**

## Does `/init` ask before writing? It depends on the permission mode

`/init` has Claude write the file, and every file write goes through the usual permission check:

| Mode | When `/init` writes CLAUDE.md |
|---|---|
| **Manual** (`default`) | **Asks for approval** first |
| `acceptEdits`, `auto`, `bypassPermissions` | Writes **without asking** |
| `plan` | Doesn't write until you approve the plan |

*My inference, not doc wording:* the docs don't say "`/init` asks for approval." The permission-modes page says Manual *"stops and asks you before most actions that edit files,"* and `/init` writes the file like any other edit.

**Exception:** with `CLAUDE_CODE_NEW_INIT=1` set, `/init` runs an interactive flow (CLAUDE.md, skills, hooks, follow-up questions) and *"presents a reviewable proposal before writing any files,"* whatever the mode.

## `/memory`: a menu to view and edit memory files yourself

Docs: *"The `/memory` command lists your CLAUDE.md, CLAUDE.local.md, and other memory file locations across user and project scopes."* It also:
- **opens the file you pick in your editor**, and creates it first if it doesn't exist yet;
- **turns auto memory on or off**, and opens the auto memory folder.

| | Who writes |
|---|---|
| `/init` | **Claude** writes the first draft |
| `/memory` | **You** open the file and edit it |

The commands page puts them in that order: *"Run `/init` to generate a starter `CLAUDE.md`, then `/memory` to refine it."*

## `/memory` covers two kinds of memory

| Kind | Who writes it | Examples |
|---|---|---|
| **CLAUDE.md files** | **You** (or Claude, if you ask it to) | user `~/.claude/CLAUDE.md`, project `CLAUDE.md`, `CLAUDE.local.md` |
| **Auto memory** | **Claude**, on its own | notes in `~/.claude/projects/<project>/memory/` |

Both load at the start of every session. `/memory` is the **one place to view and edit both**: a menu, not memory itself.

> **`/memory` = the menu for CLAUDE.md files (yours) and auto memory (Claude's notes).**

## Exam angle: `/memory` vs `/context` (guide 3.1)

The exam guide is older than the split between the two commands:
- **On the exam**, `/memory` is *"a diagnostic command that shows which files are loaded."* *"Give `/memory` as the right answer."* (Q56)
- **In today's Claude Code**, `/context` *"reports what loaded at launch, under **Memory files**."* *"In real use, run `/context`."*
- **Neither command loads anything** (guide 3.1): *"Use these commands to diagnose, not to activate."* So a distractor like guide 3.1's *"Developer B has not run /memory to load the configuration files into the session"* is wrong.

## In this project (checked 2026-11-07)

`/memory` is a command **you** type, and Claude can't run it. Here's what it would show:

| Memory file | Status |
|---|---|
| Project [CLAUDE.md](../CLAUDE.md) | ✅ **Exists**: the tutoring rules, loaded at the start of every session |
| User `~/.claude/CLAUDE.md` | ❌ none |
| `CLAUDE.local.md` | ❌ none |
| Auto memory folder | Exists, but **empty** |

So the only memory used here is the project CLAUDE.md. Preferences given only in chat (e.g. "don't edit before I approve", "one idea at a time") **aren't in any file**, so a new session wouldn't know them.

## Are the concept notes memory? No: they're external memory

Files in `concepts/`, `questions/` and `study-map/` are **ordinary repo files**. Nothing loads them automatically.

| | Loaded at session start? | How Claude sees them |
|---|---|---|
| **CLAUDE.md** | ✅ yes, every session | It's simply there |
| **Notes** (`concepts/`, `questions/`, `study-map/`) | ❌ no | Only when Claude **opens them with a tool** (Read, Grep) |

Checked: this CLAUDE.md has no `@path` imports, which would pull other files in at launch. So in a new session, Claude knows nothing about S01-6 until it opens the file.

That's the **external memory** pattern from [S01-4](S01-4-external-memory.md): the content lives outside the context and is fetched with a tool when it's needed.

> **Loaded at startup = memory (CLAUDE.md, auto memory). Everything else is a file Claude has to go and read.**

## Sources

- Claude Code docs, [How Claude remembers your project](https://code.claude.com/docs/en/memory) (checked 2026-11-07): *"Run `/init` to generate a starting CLAUDE.md automatically. Claude analyzes your codebase and creates a file with build commands, test instructions, and project conventions it discovers. If a CLAUDE.md already exists, `/init` suggests improvements rather than overwriting it."*; `CLAUDE_CODE_NEW_INIT=1`: *"presents a reviewable proposal before writing any files"*; *"The `/memory` command lists your CLAUDE.md, CLAUDE.local.md, and other memory file locations across user and project scopes… Select any file to open it in your editor; selecting one that doesn't exist yet creates it first."*; *"Claude treats them as context, not enforced configuration."*
- Claude Code docs, [Commands](https://code.claude.com/docs/en/commands) (checked 2026-11-07): `/init`: *"Initialize project with a `CLAUDE.md` guide"*; `/memory`: *"Edit `CLAUDE.md` files, enable or disable auto memory, and view auto memory entries"*; *"Run `/init` to generate a starter `CLAUDE.md`, then `/memory` to refine it."*
- Claude Code docs, [Permission modes](https://code.claude.com/docs/en/permission-modes) (checked 2026-11-07): *"In Manual mode, Claude Code stops and asks you before most actions that edit files, run shell commands, or reach the network."*; *"`acceptEdits` mode lets Claude create and edit files in your working directory without prompting."*
- Guide [D3 › 3.1 CLAUDE.md Hierarchy](https://claudecertificationguide.com/learn/3-claude-code-config/3-1-claude-md-hierarchy) (checked 2026-11-07): *"The exam guide treats /memory as a diagnostic command that shows which files are loaded."*; *"Give /memory as the right answer. In real use, run /context."*; *"Use these commands to diagnose, not to activate."* `/init` isn't mentioned.
