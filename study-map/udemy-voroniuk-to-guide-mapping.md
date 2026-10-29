# Udemy (Anton Voroniuk) course → Exam guide mapping

- **Udemy course:** "Claude Certified Architect Foundations: Complete Course" by Anton Voroniuk / Anton Pavelko. 11 sections, 106 lectures, 8h 50m, 9 section quizzes, 2 practice tests. Last updated 10/2026, rated 4.6 from 1,113 ratings.
  https://www.udemy.com/course/claude-certified-architect-foundations-complete-course/
- **Guide:** https://claudecertificationguide.com/learn
- **CBT column:** skill numbers from [cbt-nuggets-to-guide-mapping.md](cbt-nuggets-to-guide-mapping.md)
- **Method:** matched by lecture title. **#NN** = the Udemy lecture number.

**Result: every one of the 30 guide lessons has at least one Udemy lecture.** This includes the 6 that CBT doesn't cover (marked ⭐).

---

## Udemy sections & quizzes

| Sec | Lectures | Section / quiz title |
|---|---|---|
| S1 | #1–9 | Quiz 1: Foundations: Claude Ecosystem, Exam Overview & How LLMs Actually Work |
| S2 | #10–16 | Quiz 2: Claude API Basics: Requests, Conversation History, Limits & Code Rules |
| S3 | #17–24 | Quiz 3: Prompt Engineering: Test Sets, Explicit Criteria & Few-Shot Examples |
| S4 | #25–37 | Quiz 4: Structured Output: Tool Use, JSON Schemas, Validation Retries & Batches |
| S5 | #38–47 | Quiz 5: Tools & MCP: The Agentic Loop, Tool Descriptions & Structured Errors |
| S6 | #48–58 | Quiz 6: Support Agent: Gates, Hooks, Escalation Rules & Case Facts |
| S7 | #59–70 | Quiz 7: Multi-Agent Systems: Coordinators, Subagents, Context & Source Conflicts |
| S8 | #71–91 | Quiz 8: Claude Code Setup: CLAUDE.md, Path Rules, Skills, Plan Mode & Sessions |
| S9 | #92–98 | Quiz 9: Claude Code in CI: Independent Review, Multi-Pass Checks & JSON Output |
| S10 | #99–101 | Key Distinctions, then **Practice Test 1 & 2 (exam simulators)** |
| S11 | #102–106 | Cheat sheet, exam reasoning, "Not on the Exam" topics |

---

## Guide lesson → Udemy lectures (+ CBT skills)

### D1 Agentic Architecture & Orchestration (27%)
| Guide lesson | Udemy lectures | CBT |
|---|---|---|
| 1.1 Agentic Loops | #38 The Agentic Loop on stop_reason · #39 When the Agentic Loop Stops · #40 Practice: The Agentic Loop in Code · #48 The Agent SDK: the Same Loop, Run for You | 10, 20, 21 |
| 1.2 Multi-Agent Orchestration | #60 The Coordinator Pattern · #65 Practice: Coordinator and Subagents in Code · #69 Research Case: Coordinator Calls | 36, 39, 40 |
| 1.3 Subagent Invocation & Context Passing | #62 Spawning Subagents with Task · #63 Scoping Each Agent's Tools · #64 Passing Context Between Agents | 38, 39, 40 |
| 1.4 Workflow Enforcement & Handoff | #14 Practice: System Prompt Rules vs Code Rules · #49 Prerequisite Gates in Code · #51 Handoffs and Multi-Concern Requests | 6, 7, 39, 40 |
| 1.5 Agent SDK Hooks | #48 The Agent SDK: the Same Loop, Run for You · #50 Hooks: Intercepting Tool Calls | 6, 7, 8 |
| 1.6 Task Decomposition | #59 Workflow or Agent: How Work Is Decomposed · #61 Decomposing Research Without Gaps · #87 Phases, Subagents and Manifests | 36 |
| ⭐ 1.7 Session State & Resumption | #84 Sessions: Resume or Start Fresh · #85 Forking a Session | — |

### D2 Tool Design & MCP Integration (18%)
| Guide lesson | Udemy lectures | CBT |
|---|---|---|
| 2.1 Tool Interface Design | #25 Functions, JSON and Schemas · #41 How Claude Chooses a Tool · #42 Renaming, Splitting and Keyword Triggers | 19, 34 |
| 2.2 Structured Error Responses | #43 Structured Tool Errors · #44 Customer Messages and Empty Results · #45 Practice: Structured Tool Errors in Code | 20 |
| 2.3 Tool Distribution & Tool Choice | #27 tool_choice: auto, any and Forced · #63 Scoping Each Agent's Tools | 22 |
| 2.4 MCP Server Integration | #46 MCP: Servers, Tools and Resources · #47 Practice: BUILD - Backend Tools and an MCP Server · #77 Sharing MCP Servers Safely · #91 Dev Agent Case: MCP Servers | 5, 33, 34, 35 |
| 2.5 Built-in Tools | #79 Built-in Tools: Grep, Glob, Read, Write and Edit | 23, 24, 32 |

### D3 Claude Code Configuration & Workflows (20%)
| Guide lesson | Udemy lectures | CBT |
|---|---|---|
| 3.1 CLAUDE.md Hierarchy | #71 CLAUDE.md Levels: Who Gets What · #72 @import and the Rules Folder · #88 Claude Code Case: Team Setup | 1 |
| 3.2 Slash Commands & Skills | #74 Where Commands and Skills Live · #75 Configuring a Skill's Frontmatter · #76 Practice: Claude Code Hands-On - CLAUDE.md, Rules and a Skill | 4, 37, 38 |
| ⭐ 3.3 Path-Specific Rules | #72 @import and the Rules Folder · #73 Path-Scoped Rules with Globs | — |
| 3.4 Plan Mode vs Direct Execution | #78 Plan Mode or Direct Execution · #89 Claude Code Case: Plan and Iterate | 3 |
| ⭐ 3.5 Iterative Refinement | #82 Refinement: Examples and Tests · #83 Interviews and Issue Batching | — |
| 3.6 CI/CD Integration | #18 Git and Pull Requests · #19 CI Checks on Pull Requests · #94 Claude Code in CI: -p and JSON · #95 Context for CI Reviews and Tests · #96 Practice: Claude Code in CI - -p and JSON Output · #97 CI Case: Pipeline and Context | 8 |

### D4 Prompt Engineering & Structured Output (20%)
| Guide lesson | Udemy lectures | CBT |
|---|---|---|
| 4.1 System Prompts with Explicit Criteria | #17 Practice: Evaluate a Prompt Change on a Test Set · #20 Explicit Criteria That Reduce False Positives · #23 Exam Lens: Criteria Decisions | 13, 14, 17, 18 |
| 4.2 Few-Shot Prompting | #21 Few-Shot for Format and Judgment · #22 Few-Shot Examples for Extraction · #24 Exam Lens: Example Decisions | 17 |
| 4.3 Structured Output with Tool Use | #26 Practice: Your First Tool Call, End to End · #28 Practice: Schema-Valid Output from Claude · #35 Extraction Case: Valid Fields · #37 Practice: BUILD - ShopAssist Extracts Return Requests | 13, 19 |
| 4.4 Validation, Retry & Feedback | #29 Practice: Validate, Then Retry with the Error · #30 Retry with the Validation Error · #31 Fields That Expose Errors | 15 |
| ⭐ 4.5 Batch Processing | #33 Batch Processing: When It Fits · #34 Running a Batch: custom_id, Failures and Samples | — |
| 4.6 Multi-Instance / Multi-Pass Review | #92 Independent Review · #93 Multi-Pass Review · #98 CI Case: Review Quality | 15, 16 |

### D5 Context Management & Reliability (15%)
| Guide lesson | Udemy lectures | CBT |
|---|---|---|
| 5.1 Context Window Management | #12 Practice: The Conversation Lives in Your Code · #54 Case Facts in Long Conversations · #55 Trimming Tool Results and Lost in the Middle | 10, 26, 31 |
| ⭐ 5.2 Escalation & Ambiguity | #52 When to Escalate to a Human · #53 When Not to Escalate · #57 Support Case: Escalation Calls | — |
| 5.3 Error Propagation | #66 Failure Handling Across Agents | 40 |
| ⭐ 5.4 Codebase Exploration & Context Degradation | #80 Exploring a Codebase Step by Step · #81 The Explore Subagent · #86 Keeping Long Explorations Sharp · #90 Dev Agent Case: Exploring Code | — |
| 5.5 Human Review & Confidence Calibration | #32 Confidence Calibration and Human Review · #36 Extraction Case: Types and Review | 16 |
| 5.6 Information Provenance | #67 Keeping Claims Tied to Sources · #68 When Sources Disagree · #70 Research Case: Citing Sources | 24, 26, 30, 39 |

---

## Exam strategy lectures (no single lesson; watch near exam day)
- #3 Exam Format · #9 Six Official Production Scenarios
- #99 Key Distinctions: Agents, Tools and Context · #100 Key Distinctions: Claude Code · #101 Key Distinctions: Prompts, Output
- **Practice Test 1 & 2: Exam simulators**
- #102 CCAR-F Quick Reference: 4-Page Cheat Sheet (PDF)
- #103 How Exam Questions Reason: a Decision Routine and a Study Plan

## Background / not on the exam (low priority)
- Intro lectures #1–2, #4–8, #10–11, #13, #15–16: LLM basics, API setup, model/effort/max_tokens, etc.
- Mixed cases that span several lessons: #56 Support Case: Code and Tools · #58 Practice: BUILD - ShopAssist Becomes an Agentic System
- #104 Not on the Exam: RAG · #105 Debugging with Traces · #106 Prompt Injection. The course labels these off-exam itself, which matches CBT skills 25 and 27–29 being low priority.
