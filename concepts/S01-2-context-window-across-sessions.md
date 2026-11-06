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

## How long does Claude Code keep a session?

*(Follow-up question: "So how long does Claude Code save the context window?")*

It doesn't save a context window. It saves the **transcript**, and that is deleted automatically after **30 days** by default.

Docs ([.claude directory → Cleaned up automatically](https://code.claude.com/docs/en/claude-directory)): *"Claude Code deletes the files in the paths below once they're older than `cleanupPeriodDays`… The default is 30 days and the minimum is 1; setting `0` fails with a validation error."*

| What | Kept for | Notes |
|---|---|---|
| **Session transcripts** `projects/<project>/<session>.jsonl` (+ subagent transcripts, spilled tool results) | **30 days** (default) | After that, `--resume` / `--continue` can't find the session |
| Checkpoint snapshots (`file-history/`), plan files (`plans/`) | 30 days | Same sweep |
| **Auto memory** `projects/<project>/memory/` | **Until you delete it** | *"the sweep doesn't delete the memory files"* |
| **CLAUDE.md** | Forever | It's your own file, in your repo or `~/.claude` |
| Typed-prompt history `history.jsonl` | Until you delete it | Only the up-arrow recall, not the conversation |

**Change it** in `settings.json`, e.g. `"cleanupPeriodDays": 90` (longer) or `7` (shorter, for privacy: transcripts are **plaintext** and can contain file contents and secrets). **Delete now:** `claude purge <project-path>` (shows a plan, asks to confirm).

**This machine (checked 2026-11-05):** no `cleanupPeriodDays` in `~/.claude/settings.json`, and no project `.claude/settings*.json`, so the **30-day default** applies (unless an organisation's managed settings set it, which this check doesn't cover).

```text
$ grep -n "cleanupPeriodDays" ~/.claude/settings.json ~/.claude/settings.local.json .claude/settings.json .claude/settings.local.json
grep: ~/.claude/settings.local.json: No such file or directory
grep: .claude/settings.json: No such file or directory
grep: .claude/settings.local.json: No such file or directory
```

*(No match in `~/.claude/settings.json`; the other three files don't exist.)*

**Exam relevance:** low. The exam tests the **idea** (stateless model; history re-sent; resume vs fresh, Q45/Q72/Q92), not retention numbers.

## Follow-up, one idea at a time (2026-11-05)

**"So now you have a context window, do you?"** Yes: everything Claude Code sent for this one reply (instructions incl. CLAUDE.md, a summary of the earlier, compacted conversation, the recent messages). After the reply, I keep none of it.

- *Check: if Claude Code stopped sending the earlier messages, would I still know we discussed Q50?*
  *My answer:* "You would not know." ✅

**"So is your context window created from scratch with every question?"** Yes, rebuilt for **every request**, but not from nothing: the **same saved history** is re-sent, plus the newest message. It looks continuous; it's re-read fresh each time.

- *Check: what happens when the history gets bigger than the context window?*
  *My answer:* "It gets summarized. I'm not sure whether it's the history file itself or just the amount of text you're fed every time." ✅ **Compaction.** The **file isn't shortened**: it keeps everything and grows (13.6 MB here). What changes is what's **sent**: the summary + newer messages instead of the old ones.

- *Check: after compaction, can I still quote your very first message today exactly?*
  *My answer:* "No, you most likely won't be able to quote it, but you might have a summarized version." ✅ And that's the catch: **summaries lose exact details** (numbers, IDs, wording). That's why facts that must survive go into a **case-facts block or scratchpad file**, outside the summary (Q40, Q79, guide 5.1).

## Does resuming use a special endpoint? (2026-11-06)

**No. Resuming a session uses no special endpoint. Claude Code reloads a local file and sends it to the same normal API.**

```text
1. Claude Code reads the transcript from YOUR disk
   ~/.claude/projects/<project>/<session>.jsonl
2. It rebuilds the message list from it
   (or, if the session was compacted: the summary + the messages after it)
3. With your next message, it sends that whole list to the usual Messages API
   (POST /v1/messages) — the same endpoint as every other request
```

There's **no "resume" endpoint**, because the API is stateless (guide 5.1). The server keeps no session that it could restore. The **memory lives in the local file**, and the server simply receives a long message list.

> **Resume = read the local transcript, then re-send it. The server doesn't know this is a "resumed" session.**

*(Not to be confused with **checkpoints**: local file snapshots used to rewind code changes.)*

- *Check: if you copy the `.jsonl` file to another computer (same project path) and run `claude --resume` there, could the session continue? Why or why not?*
  *My answer:* "If that jsonl contains all the conversation history, it should be good to carry on. On the other hand, you don't have the product itself, like code or artefacts produced in the previous session, on that other computer." ✅ Excellent. Both sides covered: (1) **the conversation continues**, because the `.jsonl` is the whole "paper" and Claude Code re-sends it; (2) **the world doesn't come with it**, because code and files live in the project folder, not in the transcript.
  What makes this tricky is that the transcript still **contains old tool results**, like "I read `auth.py`, it says…". On the new computer those files may be missing or different, but Claude **believes its old reads**. It acts confidently on stale information. The fix is the same as in Q45/Q72: **tell Claude what's different**, or start fresh with a short summary.

  > **The transcript carries the conversation, not the world. Old tool results can be stale.**

## Checkpoints: automatic undo points for your files (2026-11-06)

**Checkpoints are automatic "undo points" for your files. Claude Code saves a snapshot before each prompt you send.**

```text
You: "refactor auth"     ← checkpoint 1 (files saved as they were)
  Claude edits 3 files
You: "now add logging"   ← checkpoint 2
  Claude breaks something 😬
→ /rewind  (or Esc Esc) → pick checkpoint 2 → files go back
```

When you rewind, you choose **what** goes back:
- **Restore code**: the files go back, and the conversation stays.
- **Restore conversation**: the chat goes back, and the files stay.
- **Restore both.**

The rewind menu also has **Summarize from here / up to here**: a targeted compaction.

Checkpoints are **local**, saved with the session, so `/rewind` still works after a resume. They're deleted after about 30 days, in the same sweep as transcripts.

**The catch: checkpoints only track edits made by Claude's file-editing tools.** These aren't tracked:
- Files changed **only by Bash commands**, like `rm` or `mv`
- **Your own** edits outside Claude Code
- Most **subagent** edits

> Docs: *"Not a replacement for version control."* Checkpoints give you quick undo within a session. Git is for real history.

- *Check: Claude runs `rm config.yaml` through Bash, and you then `/rewind`. Does `config.yaml` come back? Why or why not?*
  *My answer:* "It should, as it is Claude's creation and it should be stored in the checkpoint." ❌ Not in the usual case, and that's the catch. Checkpoints don't track **who** made a change. They track **which tool** made it.
  - Claude's **file-editing tools** (Edit, Write) save a snapshot first, so rewinding can undo those edits.
  - **Bash** just runs the command. No snapshot is taken, so `rm config.yaml` can't be undone by `/rewind`. Your only way back is **Git**.
  - Exception (docs): if Claude had **already edited `config.yaml` with its file tools** earlier in the conversation, the file is tracked, and `/rewind` also undoes later Bash changes to it.

  > **Checkpoints watch Claude's file tools, not Claude's shell. Commit to Git before risky Bash work.**

- *Check: Claude used Edit to change `app.py`, then you changed `app.py` by hand in VS Code. You `/rewind` to before Claude's edit. What happens to your manual change?*
  *My answer:* "You rewind it to Claude's version before your edit." 🟡 Right that **your manual change is lost**, but the file goes back further: to the snapshot taken **before Claude's edit**. Claude's edit and your change are both gone.

  ```text
  checkpoint (you rewind HERE) → app.py as it was originally
    Claude: Edit app.py
    You:    edit app.py in VS Code
  /rewind → app.py = original version   (Claude's edit ❌, your edit ❌)
  ```

  `app.py` is tracked because Claude edited it, so a restore **overwrites the whole file** with the snapshot. Checkpoints don't keep your manual edit separately. Docs: external changes are *"normally not captured, unless they happen to modify the same files."*

  > **Rewind restores the snapshot of the whole file. Anything done to it afterwards is lost, by anyone. Commit to Git first if your own edits matter.**

## What Claude Code treats as a session (2026-11-06)

**A session is one saved conversation, which means one transcript file with one session ID, tied to one project folder.** Docs: *"A session is a saved conversation tied to a project directory. Claude Code stores it locally as you work."*

```text
~/.claude/projects/<project folder>/
   29debd0b-…-ae52.jsonl   ← one session = one file = one ID
   7f3c…-91ab.jsonl        ← another session
```

| You do | Result |
|---|---|
| `claude` | **New** session: new ID, new file |
| `/clear` | **New** session. The old one stays on disk and can be resumed |
| `claude --continue` | **Same** session: the most recent one in this folder |
| `claude --resume` | **Same** session: the one you pick from a list |
| `/branch` or `--fork-session` | **New** session that starts as a copy of the old one, which stays unchanged |

> **Session = conversation, not window or process.** Closing the terminal doesn't end it. It sits on disk, ready to resume, for about 30 days.

- *Check: you run `claude` in project folder A, then later run `claude --continue` in project folder B. Do you get the session from A? Why or why not?*
  *My answer:* "If the session is associated with folder A, you should work in folder A." ✅ Right. You **don't** get A's session. `--continue` looks only in the **current folder's** sessions, so in B you get B's most recent session, or nothing if B has none. That's because sessions are filed **by project folder**, in `~/.claude/projects/<folder>/`.

  > **To resume a session, `cd` into its project folder first.**

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
- Claude Code docs, [Checkpointing](https://code.claude.com/docs/en/checkpointing) (checked 2026-11-06): *"Every prompt you send that starts a turn creates a new checkpoint"*; *"A file that only Bash commands changed isn't tracked and stays as it is. To revert it, use version control, such as Git."*; *"Not a replacement for version control."*
