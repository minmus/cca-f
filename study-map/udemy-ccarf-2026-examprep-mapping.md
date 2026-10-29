# Udemy "Claude Certified Architect (CCAR-F) - 2026 Exam Prep" → Exam guide mapping

- **Udemy course:** "Claude Certified Architect (CCAR-F) - 2026 Exam Prep". 136 lectures, ~15h (summed from lecture lengths), 18 quizzes, 2 practice exams. Rating, instructor and last-updated date not captured yet.
- **Role in study plan:** **primary course** (domain-aligned, concept-first). Use [Voroniuk](udemy-voroniuk-to-guide-mapping.md) for exam-lens cases and its 2 simulators.
- **Guide:** https://claudecertificationguide.com/learn
- **Method:** matched by lecture title, from the curriculum pasted by the user. **#NN** = Udemy lecture number.

**Result: all 30 guide lessons covered.** 3.5 Iterative Refinement is only partial (🟡).

---

## Sections & quizzes (the course mirrors the exam domains)

| Part | Lectures | Quiz | Topic |
|---|---|---|---|
| 1A | #1–9 | Quiz 1 | Agents vs workflows, the agent loop, choosing an architecture |
| 1B | #10–15 | Quiz 2 | Task decomposition, pipelines, parallel, ambiguity |
| 1C | #16–22 | Quiz 3 | Orchestrator, subagents, message schemas, handoffs, state & sessions |
| 1D | #23–31 | Quiz 4 | Errors, retries, fallbacks, programmatic enforcement, hooks, review points |
| 2A | #32–42 | Quiz 5 | Tool descriptions, input schemas, tool errors, tool scoping & selection |
| 2B | #43–49 | Quiz 6 | MCP primitives, servers, clients |
| 2C | #50–57 | Quiz 7 | MCP transports & production, built-in tools |
| 3A | #58–62 | Quiz 8 | Claude Code basics, context, permission modes |
| 3B | #63–70 | Quiz 9 | CLAUDE.md hierarchy, path rules, writing instructions |
| 3C | #71–79 | Quiz 10 | Slash commands, skills, subagents, hooks, plan mode |
| 3D | #80–82 | Quiz 11 | Hook applications, Agent SDK sessions, CI/CD |
| 4A | #83–88 | Quiz 12 | Clarity, system prompts, XML structure |
| 4B | #89–96 | Quiz 13 | Few-shot, CoT, extended thinking, sampling params, multi-turn |
| 4C | #97–106 | Quiz 14 | Structured output, validation, multi-pass review, evals, batch |
| 5A | #107–113 | Quiz 15 | Context window, summaries losing facts, fact blocks, trimming |
| 5B | #114–119 | Quiz 16 | Escalation, handoff payload, error propagation, ambiguity |
| 5C | #120–125 | Quiz 17 | Scratchpad, delegated discovery, compaction, session memory |
| 5D | #126–132 | Quiz 18 | Confidence calibration, review queues, claim-source mapping, conflicts |
| Exams | #133–136 | **Practice Exam 1 (warm-up) & 2 (moderate)** | |

---

## Guide lesson → Udemy lectures

### D1 Agentic Architecture & Orchestration (27%)
| Guide lesson | Lectures |
|---|---|
| 1.1 Agentic Loops | #4 Tool Use as the Foundation of Agency · #5 Perception, Reasoning, Action, and Observation · #6 Loop Control — Stopping, Iterating, and Escalating |
| 1.2 Multi-Agent Orchestration | #9 Choosing the Right Architecture · #16 The Orchestrator Role · #18 Multi Agent Topology and Selection · #23 Multi-Agent Cost and Fit |
| 1.3 Subagent Invocation & Context Passing | #17 Subagent Design — Scope, Authority, and Isolation · #19 Designing Agent-to-Agent Message Schemas · #75 Creating and Configuring Custom Subagents · #76 Designing Subagents for Delegation |
| 1.4 Workflow Enforcement & Handoff | #20 Handoff Protocols and Continuity Across Agents · #28 Why Prompts Are Not Sufficient for High-Stakes Paths · #29 Designing Programmatic Validation and Enforcement Layers · #31 Interruption Points and Review Workflows |
| 1.5 Agent SDK Hooks | #30 Hook Lifecycle Events · #77 Hook Lifecycle Events · #80 Real-World Hook Applications |
| 1.6 Task Decomposition | #3 Agents, Workflows, and Chatbots · #8 When Workflows Outperform Agents · #10 Breaking Complex Goals Into Actionable Subtasks · #11 Decomposition Patterns · #12 Sequential Pipelines · #13 Parallel Execution — Fan-Out, Fan-In · #14 Adaptive Planning |
| 1.7 Session State & Resumption | #21 In-Context State vs. External Memory · #22 Session Continuity Across Turns and Failures · #81 The Claude Agent SDK — Session Control · #124 Long-Conversation Coherence, Compaction, and Session Memory |

### D2 Tool Design & MCP Integration (18%)
| Guide lesson | Lectures |
|---|---|
| 2.1 Tool Interface Design | #32 How Claude Selects Tools · #33 Writing Tool Descriptions That Route Correctly · #34 Designing Tool Input Schemas · #35 Required vs. Optional Parameters, Enums, and Constraints · #36 Strict Tool Use and Valid Inputs |
| 2.2 Structured Error Responses | #37 Returning Useful Errors from Tools · #38 Tool Retry Logic and Partial Success Patterns |
| 2.3 Tool Distribution & Tool Choice | #39 Tool Scoping per Agent — Why Less Is More · #40 Selection Heuristics and Tool-Selection Failures · #41 Controlling Tool Selection · #42 Tool Search for Large Catalogs |
| 2.4 MCP Server Integration | #43 What MCP Is · #44 Tools, Resources, and Prompts — The Three MCP Primitives · #45–49 (server, tools, resources, prompts, clients) · #54 Tool Boundaries, Access Scoping, and Least Privilege · #55 MCP in Production |
| 2.5 Built-in Tools | #56 The Built-in Toolset · #57 Choosing Built-in vs Custom · #59 The Tool System — Reads, Bash, Edits, and Web Search |

### D3 Claude Code Configuration & Workflows (20%)
| Guide lesson | Lectures |
|---|---|
| 3.1 CLAUDE.md Hierarchy | #60 Starting a Project · #63 The Three-Level CLAUDE.md Hierarchy · #64 Load Order and Conflict Resolution · #65 Subdirectory CLAUDE.md Files · #69 Common CLAUDE.md Patterns for Real Teams |
| 3.2 Slash Commands & Skills | #71 Creating and Structuring Custom Slash Commands · #72 Command Design Patterns and Team Distribution · #73 What Skills Are · #74 Authoring Agent Skills |
| 3.3 Path-Specific Rules | #66 Excluding Files and Content · #67 Path-Scoped Rules for Conventions |
| 3.4 Plan Mode vs Direct Execution | #78 Plan Mode — When and Why to Plan First · #62 Claude Code Permission Modes |
| 🟡 3.5 Iterative Refinement | #79 Iterating on a Plan — Approve, Edit, Re-Plan · #68 Writing Instructions Claude Follows *(partial: see Voroniuk #82–83)* |
| 3.6 CI/CD Integration | #82 Claude Code in CI/CD Pipelines · #81 The Claude Agent SDK — Session Control |

### D4 Prompt Engineering & Structured Output (20%)
| Guide lesson | Lectures |
|---|---|
| 4.1 System Prompts with Explicit Criteria | #83 Clarity, Specificity, and the Cost of Ambiguity · #84 Instruction Decomposition and Constraint Design · #85 The System Prompt · #86 Role Assignment · #87 XML Tags · #88 Document Structure Patterns |
| 4.2 Few-Shot Prompting | #89 Why Examples Work · #90 Designing High-Quality Few-Shot Examples |
| 4.3 Structured Output with Tool Use | #97 JSON Mode and Schema-Constrained Output · #98 Schema Design for Reliable Extraction · #99 Structured Outputs Limits · #100 Using Tool Definitions to Extract Structured Data |
| 4.4 Validation, Retry & Feedback | #26 Retry Logic — When to Retry, When to Abort · #101 Validation Loops and Hallucination Prevention · #102 Validation Gates and Retry Strategy |
| 4.5 Batch Processing | #106 Batch Processing at Scale |
| 4.6 Multi-Instance / Multi-Pass Review | #103 Multi-Pass Review · #104 Building a Prompt Evaluation Workflow · #105 Test Dataset Generation and Model-Based Grading |

### D5 Context Management & Reliability (15%)
| Guide lesson | Lectures |
|---|---|
| 5.1 Context Window Management | #107 How the Context Window Works · #108 Context Window Economics · #109 Why Summaries Lose Facts · #110 Designing a Persistent Fact Block · #111 The Memory Tool · #112 The Tool-Output Bloat Problem · #113 Trim Strategies · #125 Server-Side Compaction |
| 5.2 Escalation & Ambiguity | #15 Handling Ambiguity and Incomplete Specifications · #114 When to Escalate — Trigger Design · #115 The Handoff Payload · #118 The Silent Bug — When 'Nothing' Is Ambiguous · #119 Disambiguation Patterns |
| 5.3 Error Propagation | #24 Tool, Reasoning, and Environment Errors · #25 Error Detection · #27 Fallback Chains and Graceful Degradation · #116 Where to Catch the Error · #117 Designing the Propagation Contract |
| 5.4 Codebase Exploration & Context Degradation | #120 The Scratchpad Pattern · #121 Designing a Healthy Scratchpad · #122 Why Delegate Discovery to a Subagent · #123 Delegation Mechanics · #124 Long-Conversation Coherence |
| 5.5 Human Review & Confidence Calibration | #126 Per-Field Confidence · #127 Calibrating with a Validation Set · #128 Stratified Sampling · #129 Routing Low-Confidence Output Through a Review Queue |
| 5.6 Information Provenance | #109 Why Summaries Lose Facts · #131 Claim-to-Source Mappings — Why Every Sentence Needs a Source · #132 Conflict Annotation — Surfacing Disagreement in Synthesis |

---

## Probably deeper than the exam needs (lower priority)
- #50–53 MCP transports (STDIO, StreamableHTTP), servers making LLM calls through the client, notifications and roots
- #91–94 Chain-of-thought, extended thinking, steering, temperature/top_p/top_k. Useful background, but no guide lesson matches them.
- #95–96 Multi-turn design. This overlaps with 5.1.

## Non-content
- #1, #2, #7, #70, #130 Welcome and check-ins · #133–136 practice exam briefings, wrap-up and bonus
