# Q24

## Question

You have testing conventions that must be uniformly applied, but your test files are distributed alongside source code files (e.g., Button.test.tsx next to Button.tsx). Why is creating a rule file in .claude/rules/ better than modifying the root CLAUDE.md?

- **A.** The root CLAUDE.md cannot process testing frameworks like Jest or Vitest.
- **B.** Consolidating all conventions in the root CLAUDE.md requires Claude to infer which sections apply to which files, making the application of rules probabilistic rather than deterministic.
- **C.** Rules in .claude/rules/ can natively execute shell commands prior to loading, unlike the root configuration.
- **D.** The root CLAUDE.md is strictly reserved for environment variable expansion.

<details>
<summary><b>Show answer and explanation</b></summary>

**Topic:** Test files spread across the codebase: a path-scoped rule, not root CLAUDE.md
**Domain / lesson:** D3 › 3.3 Path-Specific Rules
**Similar to:** [Q54](../../questions/Q54-path-scoped-rules-load-by-file-type/question.md) in the main set

## Answer: **B**

A rule file with `paths: ["**/*.test.tsx"]` loads **exactly when** Claude works on a test file, wherever it sits. In root CLAUDE.md the testing section loads in every session, and Claude has to work out for itself when it applies.

| Option | Verdict | Why |
|---|---|---|
| **A** Root CLAUDE.md can't process Jest/Vitest | ❌ | False. CLAUDE.md is plain text instructions; it doesn't "process" frameworks at all. |
| **B** Root forces Claude to infer what applies; a path rule doesn't | ✅ | The glob match decides **when** the testing rules load, so Claude doesn't have to guess. Less unrelated text in context also helps it follow the rules that do apply. |
| **C** Rules run shell commands before loading | ❌ | False. Rule files are Markdown instructions, not scripts. (Running commands automatically is what hooks do.) |
| **D** Root CLAUDE.md is only for env variables | ❌ | False. Root CLAUDE.md holds project instructions that apply in every session. |

**Small caveat on B's wording:** what's deterministic is **when the rule loads** (the glob match). Once loaded, a rule is still an instruction Claude follows, not a guarantee (see [Q52](../../questions/Q52-posttooluse-hook-runs-formatter/question.md)).

## Where it comes from

**Guide D3 › 3.3 Path-Specific Rules** (as recorded in Q54, Q52):
- *"A path-specific rule loads its conventions only when you edit files that match its glob patterns."* (**B**)
- *"In a glob, `**` matches across directory levels."* *"So `**/*.test.tsx` matches every test file in the codebase, wherever it is."* (**B**: the co-located test files)
- *"Root CLAUDE.md loads for every session, whichever files you edit."* (**B**)
- *"Less irrelevant context keeps the model focused on conventions that apply to the current work."* (**B**)
- *"Path-scoped rules are more token-efficient than root CLAUDE.md because they load only when you edit matching files."*
- Path rules *"stay in context as guidance."* (the caveat)

**Claude Code docs (memory):** *"A path-scoped rule loads when Claude uses the Read, Write, or Edit tool on a matching file."*

</details>
