# Learning path: the 114 questions, stage by stage

The questions in this repo were added in the order they were pasted. This file puts them in a learning order instead, alongside the guide and the three courses.

**How the order works (inductive):**
- **Bottom-up.** It starts with one API call and builds to multi-agent systems, Claude Code and CI, so each stage needs only what came before.
- **Cases first.** Each stage gives you the concrete cases first. You write the rule yourself, then check it against the cheat sheet.
- **Simple to mixed.** Within a stage, single-lesson questions come first. Questions that mix in another lesson come last.

**Why this order and not one course's order:**
- **Udemy B (Voroniuk)** is the spine. Its sections (S2 → S9) already build bottom-up, and they match the exam scenarios the questions use: extraction, support agent, multi-agent research, Claude Code, CI.
- **Udemy A** stays the primary course for watching. It's ordered by domain (D1 → D5), which puts multi-agent orchestration before tool design, so its lecture numbers jump between stages.
- **CBT** is a supplement. It has no matching skill for 1.7, 3.3, 3.5, 4.5, 5.2 or 5.4.

## How to run a stage

1. **Read** the guide lesson(s) linked at the top of the stage.
2. **Watch** the Udemy A lectures, then the Udemy B ones. CBT is optional. Each lecture is listed once, in the stage where it fits best.
3. **Answer each question cold**, in the order given:
   - Open the question in the Markdown preview (Ctrl+Shift+V in VS Code, or on GitHub). The answer and explanation are folded away.
   - Pick A–D and write a one-line why, then click **Show answer and explanation** and read the "Why the others are wrong" table.
   - Answer the check question again before you read your recorded answer.
4. **Write the stage's rule** in your own words (the prompt is given), then compare it with the cheat-sheet rows listed.
5. **Do the quizzes** listed. Each quiz sits at the first stage where you've watched all the exam lectures of its part or section.
6. **Keep a Revisit list** of anything you missed, and start the next stage with it.

**Marks next to questions:** ❌ = your recorded check answer was wrong · 🟡 = it was partly right · ⏳ = check answer still pending. Slow down on these.

**Time:** about 31 hours in all, estimated: Udemy A ~13 h, Udemy B ~7 h, questions ~11 h at ~6 min each. Lecture lengths aren't recorded, so lecture times use each course's average (Udemy A ~6.6 min, Udemy B ~5 min). Plan on one stage per study session.

**Before Stage 1 (optional, ~10 min):** Udemy B #3 Exam Format and #9 Six Official Production Scenarios.

## Overview

| Block | Stages | Udemy B section |
|---|---|---|
| **A** One call, done well | 1, 2, 3, 4 | S2–S4 |
| **B** Tools | 5, 6 | S5 |
| **C** Support agent | 7, 8 | S6 |
| **D** Multi-agent research system | 9, 10, 11, 12 | S7 |
| **E** Claude Code and developer agents | 13, 14, 15, 16, 17 | S8 |
| **F** CI/CD | 18 | S9 |

| Stage | Block | Topic | Guide lessons | Questions | ~Time |
|---|---|---|---|---|---|
| [1](#stage-1) | A | The request and the loop | 1.1, 5.1 | 3 | ~95 min |
| [2](#stage-2) | A | Prompts: explicit criteria, then examples | 4.1, 4.2 | 5 | ~125 min |
| [3](#stage-3) | A | Structured output and batch | 2.3, 4.3, 4.4, 4.5 | 6 | ~140 min |
| [4](#stage-4) | A | Human review and confidence calibration | 5.5, 4.4 | 3 | ~60 min |
| [5](#stage-5) | B | Tool interface design | 2.1, 2.3 | 9 | ~115 min |
| [6](#stage-6) | B | Tool errors | 2.2, 2.4 | 9 | ~115 min |
| [7](#stage-7) | C | Support agent I: guarantees in code | 1.4, 1.5 | 4 | ~75 min |
| [8](#stage-8) | C | Support agent II: escalation, handoff and context | 5.2, 1.4, 5.1 | 9 | ~165 min |
| [9](#stage-9) | D | Multi-agent I: decomposition and refinement loops | 1.6, 1.2 | 4 | ~90 min |
| [10](#stage-10) | D | Multi-agent II: spawning subagents and passing context | 1.3, 2.3, 1.2 | 7 | ~90 min |
| [11](#stage-11) | D | Multi-agent III: parallelism, selection and failures | 1.3, 1.2, 5.3 | 6 | ~60 min |
| [12](#stage-12) | D | Multi-agent IV: synthesis and provenance | 5.6, 5.1 | 6 | ~65 min |
| [13](#stage-13) | E | Claude Code I: where instructions live | 3.1, 3.3, 3.2, 1.5 | 6 | ~155 min |
| [14](#stage-14) | E | Claude Code II: plan, then iterate | 3.4, 3.5 | 6 | ~80 min |
| [15](#stage-15) | E | Developer agent I: built-in tools | 2.5 | 7 | ~70 min |
| [16](#stage-16) | E | Developer agent II: MCP servers | 2.4 | 6 | ~105 min |
| [17](#stage-17) | E | Developer agent III: long explorations and sessions | 5.4, 1.7 | 9 | ~120 min |
| [18](#stage-18) | F | Claude Code in CI | 3.6, 4.6 | 9 | ~115 min |

---

## Stage 1

### The request and the loop

**Guide:** [1.1 Agentic Loops](https://claudecertificationguide.com/learn/1-agentic-architecture/1-1-agentic-loops) · [5.1 Context Window Management](https://claudecertificationguide.com/learn/5-context-management/5-1-context-window-management) (the API is stateless)

**Why here:** Everything later assumes two facts. The model sees only the messages your code sends. The model, through `tool_use` and `stop_reason`, decides what happens next.

**▶ Watch (~80 min, est.):**
- **Udemy A:** #3 Agents, Workflows, and Chatbots → #4 Tool Use as the Foundation of Agency → #5 Perception, Reasoning, Action, and Observation → #6 Loop Control → #8 When Workflows Outperform Agents → #9 Choosing the Right Architecture → #21 In-Context State vs. External Memory → #22 Session Continuity Across Turns and Failures → #107 How the Context Window Works, then do **Quiz 1** (1A)
- **Udemy B:** #12 Practice: The Conversation Lives in Your Code → #38 The Agentic Loop on stop_reason → #39 When the Agentic Loop Stops → #40 Practice: The Agentic Loop in Code
- **CBT:** 10. Send Structured Messages to Claude · 20. Handle Tool Calls and Tool Results · 21. Build Multi Turn Tool Conversations

**Questions, in this order (~18 min):**

1. [Q46](../questions/Q46-stateless-api-pass-full-history/question.md)
2. [Q51](../questions/Q51-stop-reason-controls-loop/question.md) 🟡
3. [Q13](../questions/Q13-agentic-loop-model-decides-next-tool/question.md)

<details>
<summary>What each question is about (open after answering)</summary>

1. **Q46:** The API is stateless: your code sends the whole history on every request.
2. **Q51:** `stop_reason` tells the loop whether to continue or stop.
3. **Q13:** Inside the loop, the model chooses the next tool. Your code doesn't script the order.

</details>

**Write the rule yourself:** *What does the model see on each turn, and what decides whether the loop runs again?*

**Then compare** with these rows of the [cheat sheet](../cheatsheets/multi-agent-patterns.md)'s Decision cheats:

- Agent **forgets** earlier turns? (Q46)
- Must **adapt** to the unexpected

*Note: Concept notes for this stage: [S01-1-agentic-system-vs-autonomous-agent](../concepts/S01-1-agentic-system-vs-autonomous-agent.md) (workflow vs agent: who decides the next step) · [S01-2-context-window-across-sessions](../concepts/S01-2-context-window-across-sessions.md) (is the context window saved between sessions?) · [S01-3-routing-vs-orchestrator-workers](../concepts/S01-3-routing-vs-orchestrator-workers.md) (routing vs orchestrator-workers vs parallelization) · [S01-4-external-memory](../concepts/S01-4-external-memory.md) (how agents use databases outside the context window) · [S01-5-conversation-lives-in-your-code](../concepts/S01-5-conversation-lives-in-your-code.md) (Udemy B #12 lab, step by step).*

---

## Stage 2

### Prompts: explicit criteria, then examples

**Guide:** [4.1 System Prompts with Explicit Criteria](https://claudecertificationguide.com/learn/4-prompt-engineering/4-1-system-prompts) · [4.2 Few-Shot Prompting](https://claudecertificationguide.com/learn/4-prompt-engineering/4-2-few-shot-prompting)

**Why here:** A prompt is the smallest thing you can change. First learn to say exactly what you want (criteria), then what to do when words aren't enough (examples).

**▶ Watch (~95 min, est.):**
- **Udemy A:** #83 Clarity, Specificity, and the Cost of Ambiguity → #84 Instruction Decomposition and Constraint Design → #85 The System Prompt → #86 Role Assignment → #87 XML Tags → #88 Document Structure Patterns → #89 Why Examples Work → #90 Designing High-Quality Few-Shot Examples, then do **Quiz 12** (4A) and **Quiz 13** (4B)
- **Udemy B:** #17 Practice: Evaluate a Prompt Change on a Test Set → #18 Git and Pull Requests → #19 CI Checks on Pull Requests → #20 Explicit Criteria That Reduce False Positives → #21 Few-Shot for Format and Judgment → #22 Few-Shot Examples for Extraction → #23 Exam Lens: Criteria Decisions → #24 Exam Lens: Example Decisions, then do **Quiz 3**
- **CBT:** 13. Control Outputs with Prompts and Stops · 14. Apply System Prompts and Temperature · 17. Design Clear Prompts with Examples · 18. Structure Prompts with XML Tags

**Questions, in this order (~30 min):**

1. [Q122](../questions/Q122-explicit-report-vs-skip-criteria/question.md) 🟡
2. [Q126](../questions/Q126-report-all-tagged-filter-downstream/question.md) 🟡
3. [Q121](../questions/Q121-few-shot-acceptable-vs-genuine-findings/question.md)
4. [Q112](../questions/Q112-few-shot-standard-materials-format/question.md) 🟡
5. [Q98](../questions/Q98-few-shot-for-informal-measurements/question.md) 🟡

<details>
<summary>What each question is about (open after answering)</summary>

1. **Q122:** Vague "not worth addressing" → explicit report-vs-skip criteria.
2. **Q126:** "Ignore anything you're uncertain about" hides real bugs → report all, tagged, filter downstream.
3. **Q121:** A judgement that criteria can't capture → few-shot examples with their reasoning.
4. **Q112:** The same field written three ways → examples of the standard format.
5. **Q98:** A recurring informal value ("a handful") → examples that copy it as written.

</details>

**Write the rule yourself:** *When are written criteria enough, and when do you add examples?*

**Then compare** with these rows of the [cheat sheet](../cheatsheets/multi-agent-patterns.md)'s Decision cheats:

- Skip rule in a review prompt: OK or not? (Q122, Q126)
- Reviewer corrections show a recurring mistake? Inconsistent field format? Empty fields? (Q98, Q112, Q113, 4.2)

*Note: Q121, Q122 and Q126 use the CI scenario, but they test 4.1 and 4.2. Stage 18 comes back to CI.*

---

## Stage 3

### Structured output and batch

**Guide:** [2.3 Tool Distribution & Tool Choice](https://claudecertificationguide.com/learn/2-tool-design-mcp/2-3-tool-distribution-choice) (`tool_choice`) · [4.3 Structured Output with Tool Use](https://claudecertificationguide.com/learn/4-prompt-engineering/4-3-structured-output) · [4.4 Validation, Retry & Feedback](https://claudecertificationguide.com/learn/4-prompt-engineering/4-4-validation-retry-loops) · [4.5 Batch Processing](https://claudecertificationguide.com/learn/4-prompt-engineering/4-5-batch-processing)

**Why here:** Now the prompt returns data. What does a schema guarantee, and how do you run the same extraction over thousands of documents?

**▶ Watch (~105 min, est.):**
- **Udemy A:** #26 Retry Logic → #41 Controlling Tool Selection → #97 JSON Mode and Schema-Constrained Output → #98 Schema Design for Reliable Extraction → #99 Structured Outputs Limits → #100 Using Tool Definitions to Extract Structured Data → #101 Validation Loops and Hallucination Prevention → #102 Validation Gates and Retry Strategy → #106 Batch Processing at Scale
- **Udemy B:** #25 Functions, JSON and Schemas → #26 Practice: Your First Tool Call, End to End → #27 tool_choice: auto, any and Forced → #28 Practice: Schema-Valid Output from Claude → #29 Practice: Validate, Then Retry with the Error → #30 Retry with the Validation Error → #31 Fields That Expose Errors → #33 Batch Processing: When It Fits → #34 Running a Batch: custom_id, Failures and Samples
- **CBT:** 22. Route Requests Across Multiple Tools

**Questions, in this order (~36 min):**

1. [Q99](../questions/Q99-force-first-tool-then-auto/question.md)
2. [Q113](../questions/Q113-few-shot-varied-document-structures/question.md) 🟡
3. [Q120](../questions/Q120-batch-api-same-model-half-price/question.md) 🟡
4. [Q111](../questions/Q111-batch-standard-realtime-urgent/question.md)
5. [Q116](../questions/Q116-batch-if-24h-feedback-still-actionable/question.md)
6. [Q110](../questions/Q110-resubmit-only-failures-chunked/question.md)

<details>
<summary>What each question is about (open after answering)</summary>

1. **Q99:** A mandatory first step → forced `tool_choice`, then `"auto"`.
2. **Q113:** Valid JSON, but fields left empty although the document has them → examples of the varied structures.
3. **Q120:** Batch API: same model and prompts, half the price, up to 24 h.
4. **Q111:** The matching rule: batch what nobody waits for, send in real time what someone does.
5. **Q116:** The same rule in CI: batch only if feedback up to 24 h late is still useful.
6. **Q110:** Failed batch items → resubmit only those, by `custom_id`, with the fix their error needs.

</details>

**Write the rule yourself:** *What does a schema guarantee and what doesn't it? What decides batch or real time?*

**Then compare** with these rows of the [cheat sheet](../cheatsheets/multi-agent-patterns.md)'s Decision cheats:

- `tool_choice`: which setting? (Q99)
- Reviewer corrections show a recurring mistake? Inconsistent field format? Empty fields? (Q98, Q112, Q113, 4.2)
- Batch or real-time? (Q111, 4.5)
- Batch or synchronous? (Q111, Q116)
- Batch, but deadline < 24 h? (Q120)
- Batch requests failed? (Q110, 4.5)

*Note: 4.3 Structured Output and 4.4 Validation, Retry & Feedback have no question of their own yet. Q99 and Q113 touch them, so read both guide pages in this stage.*

---

## Stage 4

### Human review and confidence calibration

**Guide:** [5.5 Human Review & Confidence Calibration](https://claudecertificationguide.com/learn/5-context-management/5-5-human-review-calibration) · [4.4 Validation, Retry & Feedback](https://claudecertificationguide.com/learn/4-prompt-engineering/4-4-validation-retry-loops) (semantic errors pass the schema)

**Why here:** A schema can't catch a wrong value. With the data flowing, decide which extractions a human checks and when the rest can run unchecked.

**▶ Watch (~40 min, est.):**
- **Udemy A:** #126 Per-Field Confidence → #127 Calibrating with a Validation Set → #128 Stratified Sampling → #129 Routing Low-Confidence Output Through a Review Queue
- **Udemy B:** #32 Confidence Calibration and Human Review → #35 Extraction Case: Valid Fields → #36 Extraction Case: Types and Review → #37 Practice: BUILD - ShopAssist Extracts Return Requests *(optional)*, then do **Quiz 4**
- **CBT:** 15. Evaluate Prompts with Test Datasets · 16. Improve Graders with Model Feedback

**Questions, in this order (~18 min):**

1. [Q21](../questions/Q21-review-flag-from-calibrated-thresholds/question.md)
2. [Q95](../questions/Q95-accuracy-by-segment-before-automating/question.md)
3. [Q114](../questions/Q114-calibrated-field-confidence-routes-review/question.md) ⏳

<details>
<summary>What each question is about (open after answering)</summary>

1. **Q21:** Per-field confidence plus thresholds tested on known answers. Code sets the review flag.
2. **Q95:** High overall accuracy can hide weak segments → check by document type and field first.
3. **Q114:** Reviewers for only 20% → calibrated field-level confidence decides what they check.

</details>

**Write the rule yourself:** *How do you decide which extractions a human checks, and when is it safe to stop checking?*

**Then compare** with these rows of the [cheat sheet](../cheatsheets/multi-agent-patterns.md)'s Decision cheats:

- Confidence threshold for review? Automate high-confidence items? (Q21, Q95, 5.5)
- Limited reviewer capacity: who checks what? (Q114, 5.5)

*Note: Both Udemy courses teach batch before review (Udemy B #33–#34 before #35–#36; Udemy A 4C before 5D), so this stage follows Stage 3.*

---

## Stage 5

### Tool interface design

**Guide:** [2.1 Tool Interface Design](https://claudecertificationguide.com/learn/2-tool-design-mcp/2-1-tool-schema-design) · [2.3 Tool Distribution & Tool Choice](https://claudecertificationguide.com/learn/2-tool-design-mcp/2-3-tool-distribution-choice) (how many tools, which ones)

**Why here:** Tools are how the model acts. Their names, descriptions and inputs decide whether it picks the right one and fills it in correctly.

**▶ Watch (~65 min, est.):**
- **Udemy A:** #32 How Claude Selects Tools → #33 Writing Tool Descriptions That Route Correctly → #34 Designing Tool Input Schemas → #35 Required vs. Optional Parameters, Enums, and Constraints → #36 Strict Tool Use and Valid Inputs → #39 Tool Scoping per Agent → #40 Selection Heuristics and Tool-Selection Failures → #42 Tool Search for Large Catalogs
- **Udemy B:** #41 How Claude Chooses a Tool → #42 Renaming, Splitting and Keyword Triggers
- **CBT:** 19. Explain Tool Use and Tool Schemas

**Questions, in this order (~54 min):**

1. [Q20](../questions/Q20-parameter-descriptions/question.md)
2. [Q23](../questions/Q23-expand-minimal-tool-descriptions/question.md) ❌
3. [Q48](../questions/Q48-description-states-parameter-source/question.md)
4. [Q19](../questions/Q19-tool-accepts-available-identifier/question.md) 🟡
5. [Q17](../questions/Q17-split-unified-tool/question.md)
6. [Q30](../questions/Q30-split-generic-analyze-document/question.md)
7. [Q29](../questions/Q29-compose-mechanical-keep-judgment/question.md) 🟡
8. [Q47](../questions/Q47-merge-overlapping-tools/question.md)
9. [Q35](../questions/Q35-structured-tool-output-fields/question.md)

<details>
<summary>What each question is about (open after answering)</summary>

1. **Q20:** Parameter descriptions help Claude supply the right values.
2. **Q23:** `delete_file` vs `archive_file` with minimal descriptions → expand the descriptions.
3. **Q48:** The agent invents an `order_id` → say where the value must come from.
4. **Q19:** Change the input so the tool accepts the identifier the agent already has.
5. **Q17:** One unified tool or three specific ones → split.
6. **Q30:** A generic `analyze_document` → split it by purpose.
7. **Q29:** Combine the mechanical steps into one tool, keep the judgement with the model.
8. **Q47:** Overlapping tools cut accuracy from 88% to 71% → merge the overlap.
9. **Q35:** Return structured output fields, not a formatted text string.

</details>

**Write the rule yourself:** *When a tool is misused, when do you fix the description, split, merge or scope it?*

**Then compare** with these rows of the [cheat sheet](../cheatsheets/multi-agent-patterns.md)'s Decision cheats:

- Tool misrouting: descriptions, split, merge or scope? (Q23 vs Q17/Q30 vs Q47 vs Q04, Q74)
- Schema vs description vs code check? (Q20)
- Threshold in code or model judgment? (Q21 vs Q29)

---

## Stage 6

### Tool errors

**Guide:** [2.2 Structured Error Responses](https://claudecertificationguide.com/learn/2-tool-design-mcp/2-2-structured-error-responses) · [2.4 MCP Server Integration](https://claudecertificationguide.com/learn/2-tool-design-mcp/2-4-mcp-server-integration) (the `isError` envelope)

**Why here:** Once tools are well designed, the next question is what they return when something goes wrong, so the agent can choose the right recovery.

**▶ Watch (~60 min, est.):**
- **Udemy A:** #24 Tool, Reasoning, and Environment Errors → #25 Error Detection → #27 Fallback Chains and Graceful Degradation → #37 Returning Useful Errors from Tools → #38 Tool Retry Logic and Partial Success Patterns → #118 The Silent Bug, then do **Quiz 5** (2A)
- **Udemy B:** #43 Structured Tool Errors → #44 Customer Messages and Empty Results → #45 Practice: Structured Tool Errors in Code → #46 MCP: Servers, Tools and Resources, then do **Quiz 5**
- **CBT:** 33. Explain MCP Clients and Servers · 34. Build MCP Tools Resources and Prompts

**Questions, in this order (~54 min):**

1. [Q22](../questions/Q22-empty-result-is-not-error/question.md) 🟡
2. [Q24](../questions/Q24-structured-errors-not-exceptions/question.md)
3. [Q27](../questions/Q27-actionable-error-context/question.md)
4. [Q49](../questions/Q49-instructive-error-messages/question.md) 🟡
5. [Q34](../questions/Q34-business-errors-not-retryable/question.md) 🟡
6. [Q32](../questions/Q32-uniform-errors-add-category/question.md) 🟡
7. [Q25](../questions/Q25-mcp-protocol-vs-tool-errors/question.md) 🟡
8. [Q33](../questions/Q33-mcp-iserror-result/question.md) 🟡
9. [Q26](../questions/Q26-timeout-unknown-status-no-retry/question.md)

<details>
<summary>What each question is about (open after answering)</summary>

1. **Q22:** "No matches found" is a valid empty result, not an error.
2. **Q24:** Return structured error results instead of throwing exceptions.
3. **Q27:** A device timeout → give the agent something it can offer the user.
4. **Q49:** "Tool execution failed" → say what went wrong and what to try next.
5. **Q34:** Business errors aren't retryable. Say so, and the wasted retries stop.
6. **Q32:** Uniform "Operation failed" → add an error category.
7. **Q25:** MCP: protocol error or `isError` tool result?
8. **Q33:** How an MCP tool reports its errors back to the agent.
9. **Q26:** A timeout with unknown outcome on a side-effecting action → don't tell the agent to retry.

</details>

**Write the rule yourself:** *What must a tool result contain so the agent can choose the right recovery?*

**Then compare** with these rows of the [cheat sheet](../cheatsheets/multi-agent-patterns.md)'s Decision cheats:

- Tool result types (Q22, Q32, 2.2)
- What should an error message say? (Q49, Anthropic docs)
- Structured option vs prose option? (Q26)

---

## Stage 7

### Support agent I: guarantees in code

**Guide:** [1.4 Workflow Enforcement & Handoff](https://claudecertificationguide.com/learn/1-agentic-architecture/1-4-workflow-enforcement-handoff) · [1.5 Agent SDK Hooks](https://claudecertificationguide.com/learn/1-agentic-architecture/1-5-agent-sdk-hooks)

**Why here:** You now have a loop and tools. The first production question: which rules can rely on the prompt, and which must code enforce?

**▶ Watch (~55 min, est.):**
- **Udemy A:** #28 Why Prompts Are Not Sufficient for High-Stakes Paths → #29 Designing Programmatic Validation and Enforcement Layers → #30 Hook Lifecycle Events → #31 Interruption Points and Review Workflows → #80 Real-World Hook Applications
- **Udemy B:** #14 Practice: System Prompt Rules vs Code Rules → #48 The Agent SDK: the Same Loop, Run for You → #49 Prerequisite Gates in Code → #50 Hooks: Intercepting Tool Calls, then do **Quiz 2**
- **CBT:** 6. Implement Hooks for Safer Automation · 7. Apply Hooks to Block Sensitive Information

**Questions, in this order (~24 min):**

1. [Q28](../questions/Q28-tamper-proof-threshold-in-tool/question.md) 🟡
2. [Q31](../questions/Q31-refund-threshold-hook/question.md)
3. [Q50](../questions/Q50-post-loop-fallback-escalation/question.md) 🟡
4. [Q18](../questions/Q18-meaningful-confirmation/question.md) ❌

<details>
<summary>What each question is about (open after answering)</summary>

1. **Q28:** A tamper-proof $500 approval threshold lives in the tool's code.
2. **Q31:** Refunds over $500 must always escalate → a hook.
3. **Q50:** `max_turns` hit mid-dispute → code after the loop guarantees resolution or handoff.
4. **Q18:** Users approve 98% in 2 seconds → show what the human needs to judge.

</details>

**Write the rule yourself:** *Which rules can live in the prompt, and which must live in code? Where in the code?*

**Then compare** with these rows of the [cheat sheet](../cheatsheets/multi-agent-patterns.md)'s Decision cheats:

- Must **always** happen (money, compliance)
- Where to enforce a must-hold rule? (Q28)
- Which hook? (Q31, Q42, Q52)
- Agentic run hits its turn/iteration cap? (Q06, Q118)
- Is a review / confirmation step working? (Q18)

---

## Stage 8

### Support agent II: escalation, handoff and context

**Guide:** [5.2 Escalation & Ambiguity Resolution](https://claudecertificationguide.com/learn/5-context-management/5-2-escalation-ambiguity) · [1.4 Workflow Enforcement & Handoff](https://claudecertificationguide.com/learn/1-agentic-architecture/1-4-workflow-enforcement-handoff) (the handoff) · [5.1 Context Window Management](https://claudecertificationguide.com/learn/5-context-management/5-1-context-window-management)

**Why here:** With the guarantees in place, the agent still has to judge when a human should take over, what to hand them, and what must survive a long conversation.

**▶ Watch (~110 min, est.):**
- **Udemy A:** #15 Handling Ambiguity and Incomplete Specifications → #20 Handoff Protocols and Continuity Across Agents → #108 Context Window Economics → #109 Why Summaries Lose Facts → #110 Designing a Persistent Fact Block → #111 The Memory Tool → #112 The Tool-Output Bloat Problem → #113 Trim Strategies → #114 When to Escalate → #115 The Handoff Payload → #119 Disambiguation Patterns → #125 Server-Side Compaction, then do **Quiz 15** (5A)
- **Udemy B:** #51 Handoffs and Multi-Concern Requests → #52 When to Escalate to a Human → #53 When Not to Escalate → #54 Case Facts in Long Conversations → #55 Trimming Tool Results and Lost in the Middle → #56 Support Case: Code and Tools *(optional)* → #57 Support Case: Escalation Calls → #58 Practice: BUILD - ShopAssist Becomes an Agentic System *(optional)*, then do **Quiz 6**
- **CBT:** 31. Optimize Prompts with Caching

**Questions, in this order (~54 min):**

1. [Q44](../questions/Q44-explicit-human-request-escalate-now/question.md)
2. [Q43](../questions/Q43-frustration-offer-resolve-or-escalate/question.md)
3. [Q16](../questions/Q16-escalation-triggers/question.md)
4. [Q37](../questions/Q37-state-assumptions-not-interrogate/question.md) ❌
5. [Q14](../questions/Q14-escalation-handoff-summary/question.md)
6. [Q15](../questions/Q15-mid-process-escalation-handoff/question.md) 🟡
7. [Q38](../questions/Q38-sliding-window-recent-turns/question.md) 🟡
8. [Q40](../questions/Q40-multi-issue-case-facts-layer/question.md)
9. [Q42](../questions/Q42-trim-tool-outputs-relevant-fields/question.md)

<details>
<summary>What each question is about (open after answering)</summary>

1. **Q44:** "I want to talk to a real person NOW" → escalate in that reply.
2. **Q43:** Frustrated customer, straightforward case → acknowledge, offer to resolve now or escalate.
3. **Q16:** Which escalation triggers are reliable, and why self-rated confidence isn't one.
4. **Q37:** Ambiguous request → assume, state the assumption, offer to adjust.
5. **Q14:** What to pass to a human on escalation.
6. **Q15:** Mid-process escalation: what to hand over.
7. **Q38:** Long conversations slow down → size the history from the usage data.
8. **Q40:** Three issues in one session → a persistent, structured case-facts layer.
9. **Q42:** Tool outputs fill the context → trim them to the relevant fields.

</details>

**Write the rule yourself:** *When does the agent escalate, and what goes with it? What must survive a long conversation, and how?*

**Then compare** with these rows of the [cheat sheet](../cheatsheets/multi-agent-patterns.md)'s Decision cheats:

- Escalate or resolve? (Q16 vs Q43 vs Q44)
- Instruct or gate? (Q16)
- Ambiguous request: ask or assume? (Q37 vs 5.2)
- Exact facts must survive summaries
- Sliding window or case facts layer? (Q38 vs Q40)
- Long conversation: how much history to send? (Q38)
- Verbose tool results? (Q42)

*Note: The cheat sheet's "Human escalation handoff: the 5 fields" section goes with Q14 and Q15.*

---

## Stage 9

### Multi-agent I: decomposition and refinement loops

**Guide:** [1.6 Task Decomposition](https://claudecertificationguide.com/learn/1-agentic-architecture/1-6-task-decomposition) · [1.2 Multi-Agent Orchestration](https://claudecertificationguide.com/learn/1-agentic-architecture/1-2-orchestration-patterns) (iterative refinement loops)

**Why here:** Before any subagent runs, someone decides how the work is split: a fixed pipeline, or a plan that changes as results come in.

**▶ Watch (~70 min, est.):**
- **Udemy A:** #10 Breaking Complex Goals Into Actionable Subtasks → #11 Decomposition Patterns → #12 Sequential Pipelines → #13 Parallel Execution → #14 Adaptive Planning → #16 The Orchestrator Role → #18 Multi Agent Topology and Selection → #23 Multi-Agent Cost and Fit, then do **Quiz 2** (1B) and **Quiz 4** (1D)
- **Udemy B:** #59 Workflow or Agent: How Work Is Decomposed → #60 The Coordinator Pattern → #61 Decomposing Research Without Gaps
- **CBT:** 36. Design Workflows and Agent Systems

**Questions, in this order (~24 min):**

1. [Q73](../questions/Q73-same-steps-every-pr-prompt-chaining/question.md) 🟡
2. [Q75](../questions/Q75-intermittent-500-dynamic-decomposition/question.md) 🟡
3. [Q06](../questions/Q06-coordinator-gap-loop/question.md)
4. [Q11](../questions/Q11-analysis-gap-feedback-loop/question.md)

<details>
<summary>What each question is about (open after answering)</summary>

1. **Q73:** The same three review steps for every PR → prompt chaining, a fixed pipeline.
2. **Q75:** Intermittent 500s in unknown components → dynamic, adaptive decomposition.
3. **Q06:** The coordinator ships reports with gaps → evaluate, re-delegate, re-synthesise, with a cap.
4. **Q11:** Analysis finds gaps the strict pipeline can't act on → an explicit gap signal and a loop.

</details>

**Write the rule yourself:** *When is a fixed pipeline right, and when must the plan adapt to what comes back?*

**Then compare** with these rows of the [cheat sheet](../cheatsheets/multi-agent-patterns.md)'s Decision cheats:

- Which decomposition pattern? (Q73, Q75, 1.6)
- Results incomplete
- Loop can't converge

*Note: Q73 and Q75 use the developer-tools scenario. Both Udemy courses teach decomposition at the start of their multi-agent sections, so they open this block.*

---

## Stage 10

### Multi-agent II: spawning subagents and passing context

**Guide:** [1.3 Subagent Invocation & Context Passing](https://claudecertificationguide.com/learn/1-agentic-architecture/1-3-subagent-invocation-context) · [2.3 Tool Distribution & Tool Choice](https://claudecertificationguide.com/learn/2-tool-design-mcp/2-3-tool-distribution-choice) (tool scoping) · [1.2 Multi-Agent Orchestration](https://claudecertificationguide.com/learn/1-agentic-architecture/1-2-orchestration-patterns) (hub and spoke)

**Why here:** The mechanics: how a subagent is started, what it knows when it starts, and which tools it gets.

**▶ Watch (~45 min, est.):**
- **Udemy A:** #17 Subagent Design → #19 Designing Agent-to-Agent Message Schemas → #75 Creating and Configuring Custom Subagents → #76 Designing Subagents for Delegation, then do **Quiz 3** (1C)
- **Udemy B:** #62 Spawning Subagents with Task → #63 Scoping Each Agent's Tools → #64 Passing Context Between Agents → #65 Practice: Coordinator and Subagents in Code
- **CBT:** 39. Build an Agentic Multimedia Article System: Part 1

**Questions, in this order (~42 min):**

1. [Q36](../questions/Q36-coordinator-missing-task-tool/question.md)
2. [Q07](../questions/Q07-context-flows-through-coordinator/question.md)
3. [Q10](../questions/Q10-missing-context-in-subagent-prompt/question.md) 🟡
4. [Q12](../questions/Q12-include-complete-findings-in-prompt/question.md)
5. [Q05](../questions/Q05-goals-not-procedures-delegation/question.md) 🟡
6. [Q02](../questions/Q02-context-passing-source-index/question.md)
7. [Q04](../questions/Q04-tool-scoping-per-subagent/question.md)

<details>
<summary>What each question is about (open after answering)</summary>

1. **Q36:** The coordinator talks about delegating, but no subagent runs → `Task` missing from `allowedTools`.
2. **Q07:** Information flows through the coordinator, written into the next prompt.
3. **Q10:** "No research findings were provided" → the coordinator didn't pass them.
4. **Q12:** Give synthesis the complete findings, directly in its prompt.
5. **Q05:** Over-specified instructions → delegate goals and quality criteria, not steps.
6. **Q02:** Pass the findings plus a structured source index, not everything.
7. **Q04:** Subagents call tools outside their role → 4–5 scoped tools per role.

</details>

**Write the rule yourself:** *What does a subagent know when it starts, and what must the coordinator write into its prompt?*

**Then compare** with these rows of the [cheat sheet](../cheatsheets/multi-agent-patterns.md)'s Decision cheats:

- "Pass complete findings" vs "don't pass everything"?
- Subagent **never runs** vs **runs without info** vs **too slow**? (Q36, Q84)
- Subagent occasionally needs another role's capability
- Next agent could get empty or broken input

*Note: The cheat sheet's "3 master rules" (rule 2) and "Handoff prompt template" go with this stage.*

---

## Stage 11

### Multi-agent III: parallelism, selection and failures

**Guide:** [1.3 Subagent Invocation & Context Passing](https://claudecertificationguide.com/learn/1-agentic-architecture/1-3-subagent-invocation-context) (parallel spawning) · [1.2 Multi-Agent Orchestration](https://claudecertificationguide.com/learn/1-agentic-architecture/1-2-orchestration-patterns) (which subagents) · [5.3 Error Propagation](https://claudecertificationguide.com/learn/5-context-management/5-3-error-propagation)

**Why here:** With the mechanics clear, the coordinator makes decisions. Run subagents in parallel? Which ones does a query need? Delegate at all? What if one fails?

**▶ Watch (~25 min, est.):**
- **Udemy A:** #116 Where to Catch the Error → #117 Designing the Propagation Contract, then do **Quiz 16** (5B)
- **Udemy B:** #66 Failure Handling Across Agents → #69 Research Case: Coordinator Calls
- **CBT:** 40. Build an Agentic Multimedia Article System: Part 2

**Questions, in this order (~36 min):**

1. [Q84](../questions/Q84-parallel-subagents-from-the-coordinator/question.md) 🟡
2. [Q115](../questions/Q115-parallel-task-calls-one-response/question.md) 🟡
3. [Q83](../questions/Q83-coordinator-selects-subagents-per-query/question.md) ❌
4. [Q39](../questions/Q39-coordinator-answers-simple-followups/question.md)
5. [Q67](../questions/Q67-subagent-timeout-structured-error-context/question.md) 🟡
6. [Q41](../questions/Q41-partial-success-honest-next-step/question.md)

<details>
<summary>What each question is about (open after answering)</summary>

1. **Q84:** 12 precedents analysed one by one → parallel subagents from the coordinator.
2. **Q115:** Parallel means both `Task` calls in one response.
3. **Q83:** Simple facts go through all four subagents → the coordinator selects per query.
4. **Q39:** A summary of findings it already has → the coordinator answers directly.
5. **Q67:** A subagent times out → structured error context back to the coordinator.
6. **Q41:** One step fails mid-resolution → deliver what worked, be honest, offer a next step.

</details>

**Write the rule yourself:** *When does the coordinator spawn subagents, how many and how, and what comes back when one fails?*

**Then compare** with these rows of the [cheat sheet](../cheatsheets/multi-agent-patterns.md)'s Decision cheats:

- Delegate or do it directly? Which subagents? (Q39, Q83)
- Exact count / sum over findings? (Q39 check)
- Subagent fails: what goes back to the coordinator? (Q67, 5.3)
- One step of a resolution fails? (Q41, 5.3)

*Note: Q41 is a support-agent case. It sits here because it pairs with Q67 on 5.3.*

---

## Stage 12

### Multi-agent IV: synthesis and provenance

**Guide:** [5.6 Information Provenance](https://claudecertificationguide.com/learn/5-context-management/5-6-information-provenance) · [5.1 Context Window Management](https://claudecertificationguide.com/learn/5-context-management/5-1-context-window-management) (laying out a long context)

**Why here:** The last step of the research system: the final report must keep every claim tied to its source, including when sources disagree.

**▶ Watch (~30 min, est.):**
- **Udemy A:** #131 Claim-to-Source Mappings → #132 Conflict Annotation, then do **Quiz 18** (5D)
- **Udemy B:** #67 Keeping Claims Tied to Sources → #68 When Sources Disagree → #70 Research Case: Citing Sources, then do **Quiz 7**
- **CBT:** 24. Compare Web Search and RAG · 26. Explain RAG and Document Chunking · 30. Apply Image and PDF Inputs

**Questions, in this order (~36 min):**

1. [Q01](../questions/Q01-source-attribution-multi-agent/question.md)
2. [Q09](../questions/Q09-synthesis-preserve-merge-mappings/question.md)
3. [Q08](../questions/Q08-conflicting-findings-annotation/question.md)
4. [Q82](../questions/Q82-dates-turn-conflicts-into-trends/question.md) 🟡
5. [Q85](../questions/Q85-render-each-content-type-appropriately/question.md) 🟡
6. [Q80](../questions/Q80-delimit-sources-place-critical-at-edges/question.md)

<details>
<summary>What each question is about (open after answering)</summary>

1. **Q01:** Citations lost after summarising → content kept separate from source metadata.
2. **Q09:** Synthesis must preserve and merge the claim-source mappings.
3. **Q08:** Conflicting findings → established vs contested, each source's value kept.
4. **Q82:** 18% vs 35% → dates in the outputs turn a conflict into a trend.
5. **Q85:** Everything becomes bullet points → render each content type in its own format.
6. **Q80:** Many sources in a long context → delimit each one, put critical material at the start or end.

</details>

**Write the rule yourself:** *How does a claim keep its source from the subagent to the final report?*

**Then compare** with these rows of the [cheat sheet](../cheatsheets/multi-agent-patterns.md)'s Decision cheats:

- Prove a citation is real
- Prove a citation **supports** the claim
- Sources disagree? (Q08, Q82, 5.6)
- Synthesis output format? (Q85, 5.6)
- Lay out a long context with many sources? (Q80, 5.1/5.6)

*Note: Concept notes for this stage: [S12-1-claim-source-mapping](../concepts/S12-1-claim-source-mapping.md), [S12-2-url-vs-excerpt](../concepts/S12-2-url-vs-excerpt.md), [S12-3-extract-first-summarize-second](../concepts/S12-3-extract-first-summarize-second.md).*

---

## Stage 13

### Claude Code I: where instructions live

**Guide:** [3.1 CLAUDE.md Hierarchy](https://claudecertificationguide.com/learn/3-claude-code-config/3-1-claude-md-hierarchy) · [3.3 Path-Specific Rules](https://claudecertificationguide.com/learn/3-claude-code-config/3-3-path-specific-rules) · [3.2 Slash Commands & Skills](https://claudecertificationguide.com/learn/3-claude-code-config/3-2-slash-commands-skills) · [1.5 Agent SDK Hooks](https://claudecertificationguide.com/learn/1-agentic-architecture/1-5-agent-sdk-hooks) (hooks in Claude Code)

**Why here:** The same ideas move into Claude Code. Instructions are guidance (CLAUDE.md, rules, commands), and hooks are the guarantee.

**▶ Watch (~120 min, est.):**
- **Udemy A:** #59 The Tool System → #60 Starting a Project → #63 The Three-Level CLAUDE.md Hierarchy → #64 Load Order and Conflict Resolution → #65 Subdirectory CLAUDE.md Files → #66 Excluding Files and Content → #67 Path-Scoped Rules for Conventions → #69 Common CLAUDE.md Patterns for Real Teams → #71 Creating and Structuring Custom Slash Commands → #72 Command Design Patterns and Team Distribution → #73 What Skills Are → #74 Authoring Agent Skills → #77 Hook Lifecycle Events
- **Udemy B:** #71 CLAUDE.md Levels: Who Gets What → #72 @import and the Rules Folder → #73 Path-Scoped Rules with Globs → #74 Where Commands and Skills Live → #75 Configuring a Skill's Frontmatter → #76 Practice: Claude Code Hands-On - CLAUDE.md, Rules and a Skill → #88 Claude Code Case: Team Setup
- **CBT:** 1. Set Up Claude Code and Configure Projects · 2. Define Claude Code and Coding Assistants · 4. Create Custom Commands for Repeatable Workflows · 37. Create Reusable Agent Skills · 38. Build a Skill-Aware Agent

**Questions, in this order (~36 min):**

1. [Q56](../questions/Q56-memory-check-loaded-files-first/question.md) 🟡
2. [Q54](../questions/Q54-path-scoped-rules-load-by-file-type/question.md) 🟡
3. [Q60](../questions/Q60-per-package-selective-imports/question.md) 🟡
4. [Q58](../questions/Q58-at-reference-example-files/question.md) 🟡
5. [Q65](../questions/Q65-review-checklist-as-slash-command/question.md) 🟡
6. [Q52](../questions/Q52-posttooluse-hook-runs-formatter/question.md)

<details>
<summary>What each question is about (open after answering)</summary>

1. **Q56:** A convention followed only sometimes → first check which files are loaded (`/memory`).
2. **Q54:** Rules for Terraform files only → path-scoped `.claude/rules/`.
3. **Q60:** Monorepo standards with no naming pattern → each package `@`-imports what applies.
4. **Q58:** A one-off task with known example files → `@`-reference them in the prompt.
5. **Q65:** A checklist pasted at every review → a `/review` slash command.
6. **Q52:** "Always run Prettier" still slips 15% → a PostToolUse hook (Stage 7's rule again).

</details>

**Write the rule yourself:** *Where does each kind of instruction live, and when is an instruction not enough?*

**Then compare** with these rows of the [cheat sheet](../cheatsheets/multi-agent-patterns.md)'s Decision cheats:

- Claude follows CLAUDE.md only sometimes? (Q56, 3.1)
- Path rules or per-package imports? (Q54 vs Q60)
- Where does a Claude Code convention go? (Q54, 3.1/3.3)
- Show Claude a pattern to follow, **one task only**? (Q58)
- Same instructions pasted for one kind of task? (Q65, 3.2)
- Which hook? (Q31, Q42, Q52)

*Note: Concept notes for this stage: [S13-1-init-and-memory-commands](../concepts/S13-1-init-and-memory-commands.md) (`/init` drafts CLAUDE.md, `/memory` opens memory files; what loads at startup and what doesn't) · [S13-2-claude-code-vs-claude](../concepts/S13-2-claude-code-vs-claude.md) (the model decides, the Claude Code harness supplies and runs the tools).*

---

## Stage 14

### Claude Code II: plan, then iterate

**Guide:** [3.4 Plan Mode vs Direct Execution](https://claudecertificationguide.com/learn/3-claude-code-config/3-4-plan-mode-execution) · [3.5 Iterative Refinement](https://claudecertificationguide.com/learn/3-claude-code-config/3-5-iterative-refinement)

**Why here:** With the configuration in place, how you work with Claude Code: when to plan first, and how to steer it towards the result you want.

**▶ Watch (~45 min, est.):**
- **Udemy A:** #62 Claude Code Permission Modes → #68 Writing Instructions Claude Follows → #78 Plan Mode → #79 Iterating on a Plan, then do **Quiz 8** (3A) and **Quiz 9** (3B) and **Quiz 10** (3C)
- **Udemy B:** #78 Plan Mode or Direct Execution → #82 Refinement: Examples and Tests → #83 Interviews and Issue Batching → #89 Claude Code Case: Plan and Iterate
- **CBT:** 3. Apply Planning to Guide Code Changes

**Questions, in this order (~36 min):**

1. [Q53](../questions/Q53-direct-execution-for-clear-small-change/question.md)
2. [Q66](../questions/Q66-clear-stack-trace-direct-execution/question.md) 🟡
3. [Q55](../questions/Q55-tests-first-share-failures/question.md) ❌
4. [Q63](../questions/Q63-test-case-with-null-example/question.md)
5. [Q64](../questions/Q64-interview-pattern-unfamiliar-domain/question.md) 🟡
6. [Q86](../questions/Q86-interacting-issues-one-detailed-message/question.md) 🟡

<details>
<summary>What each question is about (open after answering)</summary>

1. **Q53:** One conditional in one function → direct execution, not plan mode.
2. **Q66:** A clear stack trace in an unfamiliar module → still direct execution.
3. **Q55:** A complex algorithm → write the tests first, share the failures.
4. **Q63:** Nulls mishandled → a test case with nulls and the expected output.
5. **Q64:** An unfamiliar domain → have Claude interview you before it implements.
6. **Q86:** Formatting issues that interact → one detailed message, not one fix at a time.

</details>

**Write the rule yourself:** *When do you plan first? Which refinement technique fits which problem?*

**Then compare** with these rows of the [cheat sheet](../cheatsheets/multi-agent-patterns.md)'s Decision cheats:

- Plan mode or direct execution? (Q53, 3.4)
- Iterating with Claude Code: which technique? (Q55, Q63, Q86, 3.5)

---

## Stage 15

### Developer agent I: built-in tools

**Guide:** [2.5 Built-in Tools](https://claudecertificationguide.com/learn/2-tool-design-mcp/2-5-built-in-tools)

**Why here:** An agent exploring code has Grep, Glob, Read, Write and Edit. This stage is about which one answers which question, and in what order.

**▶ Watch (~30 min, est.):**
- **Udemy A:** #56 The Built-in Toolset → #57 Choosing Built-in vs Custom
- **Udemy B:** #79 Built-in Tools: Grep, Glob, Read, Write and Edit → #80 Exploring a Codebase Step by Step → #90 Dev Agent Case: Exploring Code
- **CBT:** 23. Apply Built In Tools Safely · 32. Use Code Execution and File Storage

**Questions, in this order (~42 min):**

1. [Q134](../questions/Q134-grep-for-content-search/question.md)
2. [Q69](../questions/Q69-grep-distinctive-error-text/question.md) 🟡
3. [Q81](../questions/Q81-grep-import-statements/question.md) ❌
4. [Q132](../questions/Q132-grep-entry-points-follow-imports/question.md) 🟡
5. [Q68](../questions/Q68-trace-from-base-class-incremental-reading/question.md) 🟡
6. [Q93](../questions/Q93-collect-every-exposed-name-then-grep-each/question.md) 🟡
7. [Q76](../questions/Q76-edit-no-unique-anchor-read-write/question.md)

<details>
<summary>What each question is about (open after answering)</summary>

1. **Q134:** Find every `eval()` → Grep searches file contents (Glob matches file names).
2. **Q69:** An unknown production error → Grep for its distinctive text.
3. **Q81:** Every file that imports `@company/auth` → Grep the import statement.
4. **Q132:** Auth across 800+ files → Grep for entry points, then follow imports.
5. **Q68:** 15 caching files → find the base class, read its interface, then trace.
6. **Q93:** Wrappers rename a function → collect every exposed name, Grep each one.
7. **Q76:** Edit can't find a unique anchor → Read the file, then Write it.

</details>

**Write the rule yourself:** *Which built-in tool answers which question, and why start narrow and read incrementally?*

**Then compare** with these rows of the [cheat sheet](../cheatsheets/multi-agent-patterns.md)'s Decision cheats:

- Find code from a string you have? (Q69, Q81, Q93, 2.5)
- Exploring a large codebase? (Q132, Q134)
- Understand a large code area under context limits? (Q68, 2.5/5.4)
- Edit fails: anchor not unique? (Q76, 2.5)

---

## Stage 16

### Developer agent II: MCP servers

**Guide:** [2.4 MCP Server Integration](https://claudecertificationguide.com/learn/2-tool-design-mcp/2-4-mcp-server-integration)

**Why here:** MCP adds tools, resources and prompts from outside. They compete with the built-ins you just practised, so this comes after Stage 15.

**▶ Watch (~70 min, est.):**
- **Udemy A:** #43 What MCP Is → #44 Tools, Resources, and Prompts → #45–#49 MCP server, tools, resources, prompts and clients → #54 Tool Boundaries, Access Scoping, and Least Privilege → #55 MCP in Production, then do **Quiz 6** (2B) and **Quiz 7** (2C)
- **Udemy B:** #47 Practice: BUILD - Backend Tools and an MCP Server *(optional)* → #77 Sharing MCP Servers Safely → #91 Dev Agent Case: MCP Servers
- **CBT:** 5. Extend Claude Code with MCP Servers · 35. Test MCP Systems with the Inspector

**Questions, in this order (~36 min):**

1. [Q131](../questions/Q131-existing-jira-mcp-server/question.md) 🟡
2. [Q133](../questions/Q133-mcp-resources-content-catalogs/question.md) 🟡
3. [Q57](../questions/Q57-mcp-prompts-become-slash-commands/question.md) 🟡
4. [Q59](../questions/Q59-mcp-scopes-project-vs-user/question.md) 🟡
5. [Q74](../questions/Q74-mcp-descriptions-compete-with-built-ins/question.md)
6. [Q70](../questions/Q70-expand-mcp-description-vs-grep/question.md)

<details>
<summary>What each question is about (open after answering)</summary>

1. **Q131:** Standard Jira ticket data → use an existing Jira MCP server.
2. **Q133:** Exploratory calls across three servers → expose content catalogs as resources.
3. **Q57:** MCP prompts in Claude Code → slash commands that you run.
4. **Q59:** Team server in `.mcp.json`, personal server in `~/.claude.json`.
5. **Q74:** MCP refactoring tools ignored for Write and sed → descriptions that compete with the built-ins.
6. **Q70:** The agent greps imports instead of calling `analyze_dependencies` → expand that description.

</details>

**Write the rule yourself:** *Build or reuse a server? Tool, resource or prompt? Where is it configured, and how do you get the agent to pick it?*

**Then compare** with these rows of the [cheat sheet](../cheatsheets/multi-agent-patterns.md)'s Decision cheats:

- Build or use an MCP server? (Q131)
- MCP tool, resource or prompt? (Q57, Q133)
- MCP primitive → how it appears in Claude Code (Q57)
- Where does an MCP server go? (Q59, 2.4)
- Tool misrouting: descriptions, split, merge or scope? (Q23 vs Q17/Q30 vs Q47 vs Q04, Q74)

*Note: Q25 and Q33 (MCP errors) came in Stage 6, with the other tool errors.*

---

## Stage 17

### Developer agent III: long explorations and sessions

**Guide:** [5.4 Codebase Exploration & Context Degradation](https://claudecertificationguide.com/learn/5-context-management/5-4-codebase-exploration) · [1.7 Session State & Resumption](https://claudecertificationguide.com/learn/1-agentic-architecture/1-7-session-state-resumption)

**Why here:** Long explorations degrade. This stage covers keeping them sharp, recovering after a crash, and choosing to resume, fork or restart a session.

**▶ Watch (~65 min, est.):**
- **Udemy A:** #81 The Claude Agent SDK → #120 The Scratchpad Pattern → #121 Designing a Healthy Scratchpad → #122 Why Delegate Discovery to a Subagent → #123 Delegation Mechanics → #124 Long-Conversation Coherence, Compaction, and Session Memory, then do **Quiz 17** (5C)
- **Udemy B:** #81 The Explore Subagent → #84 Sessions: Resume or Start Fresh → #85 Forking a Session → #86 Keeping Long Explorations Sharp → #87 Phases, Subagents and Manifests, then do **Quiz 8**
- **CBT:** *no matching skill. Use the guide and Udemy.*

**Questions, in this order (~54 min):**

1. [Q78](../questions/Q78-degrading-exploration-focused-subagents/question.md)
2. [Q71](../questions/Q71-degraded-context-summary-into-subagent/question.md) 🟡
3. [Q79](../questions/Q79-scratchpad-file-for-long-sessions/question.md) 🟡
4. [Q03](../questions/Q03-crash-recovery-state-manifest/question.md)
5. [Q45](../questions/Q45-fresh-session-structured-summary/question.md)
6. [Q72](../questions/Q72-resume-and-name-changed-files/question.md) 🟡
7. [Q91](../questions/Q91-resume-subagent-and-name-the-renames/question.md) 🟡
8. [Q77](../questions/Q77-fork-session-compare-two-approaches/question.md)
9. [Q92](../questions/Q92-resume-named-session-by-name/question.md) 🟡

<details>
<summary>What each question is about (open after answering)</summary>

1. **Q78:** 8 of 45 files in, the agent is forgetting → focused subagents, the main agent coordinates.
2. **Q71:** "Typical rendering patterns" → summarise the findings, give them to a fresh subagent.
3. **Q79:** Inconsistent answers after 30+ minutes → a scratchpad file of key findings.
4. **Q03:** Pipeline crashed at 12 of 28 → a state manifest the coordinator loads.
5. **Q45:** Returning customer, stale tool results → start fresh with a structured summary.
6. **Q72:** 3 of 12 files changed overnight → resume and say which ones.
7. **Q91:** Interrupted subagent, two functions renamed → resume it and tell it about the renames.
8. **Q77:** Two approaches from one analysis → fork the session.
9. **Q92:** Yesterday's investigation, three codebases later → resume the session by its name.

</details>

**Write the rule yourself:** *What keeps a long exploration sharp? When do you resume, fork or start fresh?*

**Then compare** with these rows of the [cheat sheet](../cheatsheets/multi-agent-patterns.md)'s Decision cheats:

- Agent says "typical patterns" instead of the classes it found? (Q71, Q78, Q79, 5.4)
- Resume after crash
- Resume, fork or start fresh? (Q45, Q72, Q77, Q91, Q92, 1.7)

*Note: Q45 is a support-agent case. It sits here as the "start fresh" case of the 1.7 decision.*

---

## Stage 18

### Claude Code in CI

**Guide:** [3.6 CI/CD Integration](https://claudecertificationguide.com/learn/3-claude-code-config/3-6-cicd-integration) · [4.6 Multi-Instance & Multi-Pass Review](https://claudecertificationguide.com/learn/4-prompt-engineering/4-6-multi-pass-review)

**Why here:** The last scenario puts it all together: Claude Code with no human present, project context from files, and reviews that must be independent and complete.

**▶ Watch (~60 min, est.):**
- **Udemy A:** #82 Claude Code in CI/CD Pipelines → #103 Multi-Pass Review → #104 Building a Prompt Evaluation Workflow → #105 Test Dataset Generation and Model-Based Grading, then do **Quiz 11** (3D) and **Quiz 14** (4C)
- **Udemy B:** #92 Independent Review → #93 Multi-Pass Review → #94 Claude Code in CI: -p and JSON → #95 Context for CI Reviews and Tests → #96 Practice: Claude Code in CI - -p and JSON Output → #97 CI Case: Pipeline and Context → #98 CI Case: Review Quality, then do **Quiz 9**
- **CBT:** 8. Apply Useful Hooks and Claude Code SDK Basics

**Questions, in this order (~54 min):**

1. [Q117](../questions/Q117-append-system-prompt-keeps-tool-guidance/question.md)
2. [Q125](../questions/Q125-bare-mode-append-claude-md/question.md) 🟡
3. [Q123](../questions/Q123-claude-md-project-conventions-for-ci-review/question.md) 🟡
4. [Q124](../questions/Q124-claude-md-testing-standards-fixtures/question.md) 🟡
5. [Q128](../questions/Q128-review-md-skip-rules-evidence-bar/question.md) 🟡
6. [Q118](../questions/Q118-agentic-review-follows-cross-file-references/question.md)
7. [Q119](../questions/Q119-same-session-self-review-bias/question.md)
8. [Q129](../questions/Q129-split-review-by-concern-focused-prompts/question.md)
9. [Q130](../questions/Q130-max-tokens-truncation-split-by-files/question.md) 🟡

<details>
<summary>What each question is about (open after answering)</summary>

1. **Q117:** The CI review never reads the repo → `--system-prompt` replaced Claude Code's own instructions.
2. **Q125:** 18 s startup from auto-discovery → `--bare` + `--append-system-prompt-file ./CLAUDE.md`.
3. **Q123:** The review flags intentional patterns → put the conventions in CLAUDE.md.
4. **Q124:** 55% low-value generated tests → testing standards, fixtures and examples in CLAUDE.md.
5. **Q128:** Managed Code Review noise → REVIEW.md skip rules and an evidence requirement.
6. **Q118:** Reviews miss cross-file bugs → let the reviewer explore the codebase.
7. **Q119:** Same-session review missed bugs → an independent reviewer without the author's reasoning.
8. **Q129:** Examples help one category and hurt another → focused prompts per concern.
9. **Q130:** `report_findings` truncated at `max_tokens` → calls per file subset, merged.

</details>

**Write the rule yourself:** *How does a CI run get its project context? How do you split a review so nothing is missed?*

**Then compare** with these rows of the [cheat sheet](../cheatsheets/multi-agent-patterns.md)'s Decision cheats:

- CI startup slow, but need CLAUDE.md? (Q125)
- CLAUDE.md vs REVIEW.md vs workflow prompt? (Q128)
- How to split a review? (Q118, Q119, Q129, Q130)
- Agentic run hits its turn/iteration cap? (Q06, Q118)

*Note: The batch questions from this scenario (Q116, Q120) came in Stage 3 with the other 4.5 questions. The prompt questions (Q121, Q122, Q126) came in Stage 2.*

---

## After Stage 18

1. **Practice exams:** Udemy B #99–#101 Key Distinctions, then Practice Test 1 & 2. Then Udemy A Practice Exam 1 (warm-up) and 2 (moderate).
2. **Revisit list:** redo every question you marked, in stage order.
3. **Last day:** the cheat sheet's "Revision watch list" and Udemy B #103 How Exam Questions Reason.

## Lessons with the fewest questions so far

Counted by each question's main lesson (also-touches not counted). New questions on these lessons add the most:

- **1.1 Agentic Loops:** Q13, Q51
- **1.5 Agent SDK Hooks:** Q31, Q52
- **1.6 Task Decomposition:** Q73, Q75
- **3.1 CLAUDE.md Hierarchy:** Q56, Q60
- **3.2 Slash Commands & Skills:** Q65
- **3.3 Path-Specific Rules:** Q54
- **3.4 Plan Mode vs Direct Execution:** Q53, Q66
- **4.1 System Prompts with Explicit Criteria:** Q122, Q126
- **4.3 Structured Output with Tool Use:** none of its own (only touched by other questions)
- **4.4 Validation, Retry & Feedback:** none of its own (only touched by other questions)
- **5.3 Error Propagation:** Q41, Q67
