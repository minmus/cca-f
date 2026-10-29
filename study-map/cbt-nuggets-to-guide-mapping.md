# CBT Nuggets CCA-F course → Exam guide mapping

- **CBT course:** https://www.cbtnuggets.com/it-training/ai/claude-certified-architect-foundations-cca-f (40 skills)
- **Guide:** https://claudecertificationguide.com/learn (5 domains, 30 lessons)
- **Method:** matched **by skill title only**. The CBT page doesn't list individual video titles, so treat this as a best estimate.

**Relevance key:** 🟢 core for the exam · 🟡 partial / supporting · ⚪ background or likely off-blueprint

---

## View 1: CBT skill → guide lesson

| # | CBT skill | Guide lesson(s) | Rel. |
|---|---|---|---|
| 1 | Set Up Claude Code and Configure Projects | D3 › 3.1 CLAUDE.md Hierarchy | 🟢 |
| 2 | Define Claude Code and Coding Assistants | D3 (general intro) | 🟡 |
| 3 | Apply Planning to Guide Code Changes | D3 › 3.4 Plan Mode vs Direct Execution | 🟢 |
| 4 | Create Custom Commands for Repeatable Workflows | D3 › 3.2 Slash Commands & Skills | 🟢 |
| 5 | Extend Claude Code with MCP Servers | D2 › 2.4 MCP Server Integration | 🟢 |
| 6 | Implement Hooks for Safer Automation | D1 › 1.5 Agent SDK Hooks · D1 › 1.4 Workflow Enforcement | 🟢 |
| 7 | Apply Hooks to Block Sensitive Information | D1 › 1.5 Hooks · D1 › 1.4 Enforcement | 🟢 |
| 8 | Apply Useful Hooks and Claude Code SDK Basics | D1 › 1.5 Hooks · D3 › 3.6 CI/CD (headless/SDK) | 🟢 |
| 9 | Explain Claude API Foundations | Background for D1/D4 | ⚪ |
| 10 | Send Structured Messages to Claude | D1 › 1.1 Agentic Loops (message structure) · D5 › 5.1 | 🟡 |
| 11 | Manage Models and Controls | Background (model choice, params) | ⚪ |
| 12 | Stream Responses for Better Apps | Background | ⚪ |
| 13 | Control Outputs with Prompts and Stops | D4 › 4.1 System Prompts · D4 › 4.3 Structured Output | 🟡 |
| 14 | Apply System Prompts and Temperature | D4 › 4.1 System Prompts with Explicit Criteria | 🟢 |
| 15 | Evaluate Prompts with Test Datasets | D4 › 4.4 Validation/Retry · D4 › 4.6 Multi-Pass Review | 🟡 |
| 16 | Improve Graders with Model Feedback | D4 › 4.6 Multi-Pass Review · D5 › 5.5 Confidence Calibration | 🟡 |
| 17 | Design Clear Prompts with Examples | D4 › 4.2 Few-Shot · D4 › 4.1 | 🟢 |
| 18 | Structure Prompts with XML Tags | D4 › 4.1 System Prompts | 🟡 |
| 19 | Explain Tool Use and Tool Schemas | D2 › 2.1 Tool Interface Design · D4 › 4.3 Structured Output | 🟢 |
| 20 | Handle Tool Calls and Tool Results | D1 › 1.1 Agentic Loops · D2 › 2.2 Structured Error Responses | 🟢 |
| 21 | Build Multi Turn Tool Conversations | D1 › 1.1 Agentic Loops | 🟢 |
| 22 | Route Requests Across Multiple Tools | D2 › 2.3 Tool Distribution & Tool Choice | 🟢 |
| 23 | Apply Built In Tools Safely | D2 › 2.5 Built-in Tools | 🟢 |
| 24 | Compare Web Search and RAG | D2 › 2.5 Built-in Tools · D5 › 5.6 Provenance | 🟡 |
| 25 | Understand Tokens, Embeddings, and Vector Spaces | D5 › 5.1 (tokens only) | ⚪ |
| 26 | Explain RAG and Document Chunking | D5 › 5.1 Context Window · D5 › 5.6 Provenance | 🟡 |
| 27 | Build Embeddings and Vector Search | — (likely off-blueprint) | ⚪ |
| 28 | Implement Vector Search with Voyage AI | — (likely off-blueprint) | ⚪ |
| 29 | Implement Hybrid Retrieval Workflows | — (likely off-blueprint) | ⚪ |
| 30 | Apply Image and PDF Inputs | D5 › 5.6 Provenance (if citations covered) | 🟡 |
| 31 | Optimize Prompts with Caching | D5 › 5.1 Context Window (caching is in D5 overview) | 🟡 |
| 32 | Use Code Execution and File Storage | D2 › 2.5 Built-in Tools | 🟡 |
| 33 | Explain MCP Clients and Servers | D2 › 2.4 MCP Server Integration | 🟢 |
| 34 | Build MCP Tools Resources and Prompts | D2 › 2.4 MCP · D2 › 2.1 Tool Interface Design | 🟢 |
| 35 | Test MCP Systems with the Inspector | D2 › 2.4 MCP | 🟡 |
| 36 | Design Workflows and Agent Systems | D1 › 1.2 Orchestration · D1 › 1.6 Task Decomposition | 🟢 |
| 37 | Create Reusable Agent Skills | D3 › 3.2 Slash Commands & Skills | 🟢 |
| 38 | Build a Skill-Aware Agent | D3 › 3.2 Skills · D1 › 1.3 Subagent Context Passing | 🟡 |
| 39 | Build an Agentic Multimedia Article System: Part 1 | D1 › 1.2 / 1.3 / 1.4 · D5 › 5.6 | 🟢 |
| 40 | Build an Agentic Multimedia Article System: Part 2 | D1 › 1.2 / 1.3 / 1.4 · D5 › 5.3 Error Propagation | 🟢 |

---

## View 2: Guide lesson → CBT coverage (find the gaps)

**Coverage key:** ✅ good · 🟡 partial · ❌ none (use the guide)

### D1 Agentic Architecture & Orchestration (27%)
| Lesson | CBT skills | Coverage |
|---|---|---|
| 1.1 Agentic Loops | 10, 20, 21 | ✅ |
| 1.2 Multi-Agent Orchestration | 36, 39, 40 | ✅ |
| 1.3 Subagent Invocation & Context Passing | 38, 39, 40 | 🟡 |
| 1.4 Workflow Enforcement & Handoff | 6, 7, 39, 40 | 🟡 (hooks give enforcement; handoff likely thin) |
| 1.5 Agent SDK Hooks | 6, 7, 8 | ✅ |
| 1.6 Task Decomposition | 36 | 🟡 |
| 1.7 Session State & Resumption | — | ❌ |

### D2 Tool Design & MCP Integration (18%)
| Lesson | CBT skills | Coverage |
|---|---|---|
| 2.1 Tool Interface Design | 19, 34 | ✅ |
| 2.2 Structured Error Responses | 20 | 🟡 |
| 2.3 Tool Distribution & Tool Choice | 22 | ✅ |
| 2.4 MCP Server Integration | 5, 33, 34, 35 | ✅ |
| 2.5 Built-in Tools | 23, 24, 32 | ✅ |

### D3 Claude Code Configuration & Workflows (20%)
| Lesson | CBT skills | Coverage |
|---|---|---|
| 3.1 CLAUDE.md Hierarchy | 1 | ✅ |
| 3.2 Slash Commands & Skills | 4, 37, 38 | ✅ |
| 3.3 Path-Specific Rules | — | ❌ |
| 3.4 Plan Mode vs Direct Execution | 3 | ✅ |
| 3.5 Iterative Refinement | — | ❌ |
| 3.6 CI/CD Integration | 8 | 🟡 |

### D4 Prompt Engineering & Structured Output (20%)
| Lesson | CBT skills | Coverage |
|---|---|---|
| 4.1 System Prompts with Explicit Criteria | 13, 14, 17, 18 | ✅ |
| 4.2 Few-Shot Prompting | 17 | ✅ |
| 4.3 Structured Output with Tool Use | 13, 19 | ✅ |
| 4.4 Validation, Retry & Feedback Loops | 15 | 🟡 |
| 4.5 Batch Processing | — | ❌ |
| 4.6 Multi-Instance / Multi-Pass Review | 15, 16 | 🟡 |

### D5 Context Management & Reliability (15%)
| Lesson | CBT skills | Coverage |
|---|---|---|
| 5.1 Context Window Management | 10, 26, 31 | 🟡 |
| 5.2 Escalation & Ambiguity Resolution | — | ❌ |
| 5.3 Error Propagation in Multi-Agent Systems | 40 | 🟡 |
| 5.4 Codebase Exploration & Context Degradation | — | ❌ |
| 5.5 Human Review & Confidence Calibration | 16 | 🟡 |
| 5.6 Information Provenance | 24, 26, 30, 39 | 🟡 |

---

## Takeaways

1. **CBT is strongest on hands-on API/tool/MCP mechanics** (D2, D4 basics, hooks).
2. **CBT is weakest on architectural judgment**: D5 reliability topics (5.2, 5.4), D1 › 1.7 session resumption, D3 › 3.3 / 3.5, and D4 › 4.5 batch processing. **Use the guide for these.** They are also the most "scenario-question" style topics.
3. **Skills 25, 27–29 (embeddings / vector search / Voyage)** don't map to any guide lesson. They're useful background but **low priority for the exam**.
