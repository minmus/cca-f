# Q08

## Question

An agent executing a workflow at quicktechie.com needs to inspect the contents of a config.json file to understand current project settings. Which approach represents an architectural anti-pattern?

- **A.** Using the Read tool to parse the JSON contents directly into context.
- **B.** Invoking config.json") to output the file to the terminal.
- **C.** Using the Glob tool to verify the file's path exists before attempting to read it.
- **D.** Passing the file path directly to an approved MCP resource for validation.

*Option B's text is cut off in the source. It most likely read: Invoking `Bash("cat config.json")` to output the file to the terminal.*

<details>
<summary><b>Show answer and explanation</b></summary>

**Topic:** Read a file with the Read tool, not with `cat` through Bash
**Domain / lesson:** D2 › 2.5 Built-in Tools

## Answer: **B** (the anti-pattern)

| Option | Anti-pattern? | Why |
|---|---|---|
| **A** Read tool | ❌ No, it's the right way | Read is the built-in tool made for exactly this job: getting a file's contents into context. |
| **B** `cat` through Bash | ✅ **Yes** | It uses a general shell for a job that a dedicated built-in tool already does. Claude Code's own instructions tell Claude not to use `cat` for this and to use Read instead. |
| **C** Glob to check the path first | ❌ No | Glob finds files by name or path, which is its intended use. The step may be unnecessary, but it isn't a misuse. |
| **D** An approved MCP resource | ❌ No | Using an approved MCP integration for its purpose is a normal design choice. |

## Where it comes from

**Claude Code's Bash tool description** (the text Claude receives with the Bash tool; seen in a Claude Code session, 2026-11-19). The guide quote for this exact rule isn't recorded in this repo:
- *"IMPORTANT: Avoid using this tool to run `find`, `grep`, `cat`, `head`, `tail`, `sed`, `awk`, or `echo` commands, unless explicitly instructed or after you have verified that a dedicated tool cannot accomplish your task. Instead, use the appropriate dedicated tool…"* (**B**)

**Guide D2 › 2.5 Built-in Tools** (see [Q81](../../questions/Q81-grep-import-statements/question.md), [Q134](../../questions/Q134-grep-for-content-search/question.md)):
- *"Glob matches file paths by naming patterns: name, extension or directory structure."* (**C**)
- *"Every file read into context costs tokens, and the context window has a limit."* (**A**: read the one file you need, not more)
- From the guide's "Current state" note: *"On the exam, treat Grep and Glob as built-in tools."* (**C**)

</details>
