# Q05

## Question

Which of the following strategies are highly recommended for integrating Claude into a robust test generation workflow? Choose 2 correct answers.

- **A.** Document testing standards, valuable test criteria, and available fixtures in the project-level CLAUDE.md.
- **B.** Rely on aggregate test coverage metrics to determine if the generated tests are semantically correct.
- **C.** Share specific test failure outputs with Claude to guide progressive improvement of the implementation.
- **D.** Execute tests automatically within a PreToolUse hook to prevent Claude from saving failing code.

<details>
<summary><b>Show answer and explanation</b></summary>

**Topic:** Test generation: standards in CLAUDE.md, test failures as feedback
**Domain / lesson:** D3 › 3.6 CI/CD Integration (A) · D3 › 3.5 Iterative Refinement (C)

## Answer: **A, C**

| Option | Verdict | Why |
|---|---|---|
| **A** Testing standards, criteria and fixtures in CLAUDE.md | ✅ | Claude writes low-value tests when it doesn't know your standards or fixtures. CLAUDE.md is read on every run, in CI too, so this fixes the problem where the tests are written. |
| **B** Coverage metrics to judge semantic correctness | ❌ | Coverage shows which lines a test **ran**, not whether it **checked** the right behaviour. A test that only asserts "doesn't throw" raises coverage and proves nothing. (Reasoning, not a guide quote.) |
| **C** Share specific test failure outputs | ✅ | Test-driven iteration: each failure ("expected X, got Y") is exact feedback that tells Claude what to fix next. |
| **D** Run tests in a PreToolUse hook to block failing code | ❌ | PreToolUse runs **before** the edit is saved, so the tests would run against the old code. Blocking every failing save also breaks the test-driven loop in C, where failures are the feedback. (Reasoning, not a guide quote.) |

## Where it comes from

**Guide D3 › 3.6 CI/CD Integration** (A; checked 2026-11-03, see [Q124](../../questions/Q124-claude-md-testing-standards-fixtures/question.md)):
- *"In CI, Claude Code reads the project's CLAUDE.md files exactly as it does interactively."*
- Testing standards: *"what makes a good test, what patterns to follow, what to avoid"*
- Available fixtures: *"which test fixtures exist, how to use them, what data they contain"*
- *"Without this context, test generation in CI produces low-value boilerplate."*

**Guide D3 › 3.5 Iterative Refinement** (C; see [Q55](../../questions/Q55-tests-first-share-failures/question.md)):
- *"Write the tests first."* *"Then the developer shares the failures with Claude Code."*
- *"Each failure is concrete, unambiguous feedback: "Expected X, got Y" has a single meaning."*

**Guide D1 › 1.5 Agent SDK Hooks** (D; see [Q52](../../questions/Q52-posttooluse-hook-runs-formatter/question.md)):
- *"PostToolUse hooks transform data after execution. PreToolUse hooks enforce policy before execution."*

</details>
