# MCP prompts vs skills: both are slash commands, but they aren't the same thing

**From:** [Q57](../questions/Q57-mcp-prompts-become-slash-commands/question.md) (MCP prompts become slash commands)
**Lessons:** D2 › 2.4 MCP Server Integration · D3 › 3.2 Slash Commands & Skills

## My question

*"Are MCP prompts accessible as slash commands just like skills?"*

## Answer: same menu, different source and different rules

**What's the same:** both appear when you type `/`, and you run either one by typing its name followed by arguments. From your keyboard they feel alike.

**What's different:**

| | MCP prompt | Skill |
|---|---|---|
| **In the `/` menu as** | `/devops:deploy_checklist (MCP)`; typing `/mcp__devops__deploy_checklist` also runs it | `/deploy` |
| **Defined where** | On the **MCP server** (in any language). Claude Code discovers it when the server connects (`prompts/list`) | A **Markdown file** in your repo or home folder: `.claude/skills/<name>/SKILL.md` (project, shared through git) or `~/.claude/skills/<name>/SKILL.md` (personal). Old `.claude/commands/<name>.md` files still work |
| **Who can start it** | **Only you.** Prompts are *user-controlled* (MCP spec) | **You or Claude.** *"By default, both you and Claude can invoke any skill."* Claude loads it on its own when a request matches its `description` |
| **Restricting who starts it** | Nothing to set: always user-started | `disable-model-invocation: true` → only you · `user-invocable: false` → only Claude |
| **Arguments** | Space-separated, each one word, matched to the arguments the prompt declares | `$ARGUMENTS` (all of them), `$0`, `$1`… (by position) |
| **Where the content comes from** | The **server builds it when you run it** (`prompts/get`), so it can include live data from the server's systems | The **file's text**. Lines like `` !`git diff HEAD` `` run a shell command first and insert the output |
| **Extra settings** | Name, description, arguments | Frontmatter such as `allowed-tools`, `context: fork`, `argument-hint` (guide 3.2) |
| **How the team gets it** | Everyone who connects to the server (e.g. through a project `.mcp.json`) | Commit `.claude/skills/` to the repo |

**The difference that matters most: who can start it.** An MCP prompt runs only when a person types the command. A skill can also be picked up by Claude when the conversation matches its description.

## When to use which

| Situation | Use |
|---|---|
| A template another team maintains centrally, for many repos, filled in with live data from their systems | **MCP prompt** on their server |
| A workflow that belongs with this repo's code and is versioned with it | **Skill** in `.claude/skills/` |
| Claude should apply the workflow **on its own** when a request matches | **Skill** with a clear `description` |
| The workflow must run **only when a person starts it** | **Skill** with `disable-model-invocation: true`, or an MCP prompt (always user-started) |

**For the exam:** the guide's 3.2 is about skills (`/name`, or loaded automatically from the `description`). MCP prompts aren't in the guide; the fact to know is that they *"become available as commands in Claude Code."*

## Sources

- **Claude Code docs, *Connect Claude Code to tools via MCP* › Use MCP prompts as commands** (checked 2026-10-31): *"MCP servers can expose prompts that become available as commands in Claude Code."* *"Claude Code lists each MCP prompt as `/servername:promptname (MCP)`. Typing `/mcp__servername__promptname` also runs it."* *"Pass them space-separated after the command."* *"Prompt results are injected directly into the conversation."* The docs describe only you running them.
- **Claude Code docs, *Skills*** (checked 2026-10-31): *"By default, both you and Claude can invoke any skill. You can type `/skill-name` to invoke it directly, and Claude can load it automatically when relevant to your conversation."* *"`disable-model-invocation: true`: Claude can't invoke the skill on its own."* *"`user-invocable: false`: Only Claude can invoke the skill."* `description`: *"Claude uses this to decide when to apply the skill."* *"Custom commands have been merged into skills."* `` !`<command>` `` *"runs shell commands before the skill content is sent to Claude."*
- **MCP specification, *Prompts*:** *"Prompts are designed to be user-controlled, meaning they are exposed from servers to clients with the intention of the user being able to explicitly select them for use."*
- **Guide D3 › 3.2:** *"A skill is invoked explicitly (`/skill-name`) or automatically."* *"Skills with `disable-model-invocation: true` require explicit user invocation."*

## Check question

See [Q57's check question](../questions/Q57-mcp-prompts-become-slash-commands/question.md#check-question). It was rewritten after this note, because this note answers the original one.
