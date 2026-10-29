# Concept: Why the source index needs excerpts, not just URLs

**Domain / lessons:** D5 › 5.6 Information Provenance · D4 › 4.4 Validation/Retry · D4 › 4.6 Multi-Pass Review
**Related question:** [Q02: Context passing to the report generator](../questions/Q02-context-passing-source-index/question.md)
**Builds on:** [claim-source-mapping.md](claim-source-mapping.md) (checks 3–4)

## Check question

> **Check question:** a colleague suggests trimming answer B: keep the source index but drop the excerpts (claim → URL only) to save tokens. What does the report generator lose, and what kind of citation error becomes more likely?
> **My answer:** *"You can't tell if the claim is summarized accurately without getting the excerpt."* ✅ Right on what's lost. Made precise below, plus the name of the error.

## What a URL proves vs. what an excerpt proves

| Field | Proves | Check it enables (from [claim-source-mapping.md](claim-source-mapping.md)) |
|---|---|---|
| **URL** | The source **exists** and was retrieved | Check 1: is it in the retrieval log? (code) |
| **Excerpt** | The source **says this** | Check 3: does the quote appear in the source? (code) · Check 4: does the quote support the claim? (model) |

Drop the excerpt and checks 3 and 4 become impossible downstream. Only "the source exists" is left.

## The error that becomes more likely: the unsupported citation

An **unsupported citation** looks correct but doesn't back the claim as written: the URL is real and on topic, but the content doesn't match.

**Example:**
- Excerpt in the source: *"Revenue in the EU segment rose 12% year over year."*
- Claim in the draft: *"The market grew 12% in 2025."*

With the excerpt next to the claim, the mismatch is visible: the source covers the EU segment, not the whole market, and it doesn't name the year. With only the URL, the report generator cites it confidently and the report looks properly sourced.

Typical ways a claim drifts from its source:
- **Scope:** one segment becomes the whole market
- **Numbers:** a figure is rounded, or swapped with one from a similar source
- **Dates:** 2024 data is cited as 2025
- **Wrong source:** with 25 sources, the claim is matched to a similar but different one

## Why this is worse than a missing citation

- A **missing** citation is **visible**. A reviewer sees the gap and checks it.
- An **unsupported** citation is **silent**. It passes check 1 because the URL really was retrieved, so it looks verified, and nothing downstream can run checks 3–4.

## Why the report generator can't just check the URL itself

The report generator only knows what's in its prompt. Lesson 1.3: *"A subagent receives only what the coordinator writes into the subagent's prompt."* With only a URL, it would have to re-fetch and re-read the source, which brings back the cost that answer B was meant to save. Lesson 1.3 also warns against fixing a context-passing gap by giving the downstream agent direct tool access.

The excerpt is the **cheapest evidence that still lets the check happen**: a sentence or two instead of a whole document.

> **Rule:** A URL proves the source exists. An excerpt shows what it says. To check that a claim is faithful to its source, you need the excerpt next to the claim.

## Open check question

**Lesson 1.3 says the coordinator "must pass both in full". In one sentence, why doesn't that make option A (pass the full accumulated context) correct?**
*My answer:* (pending)

## Related course lessons

*All of these also appear in [claim-source-mapping.md](claim-source-mapping.md)'s lists. If you've already watched those, skip this. Watch times are estimates from each course's average lecture length.*

### Udemy A (primary): Claude Certified Architect (CCAR-F) - 2026 Exam Prep
| # | Lecture | Part | Why |
|---|---|---|---|
| **131** | **Claim-to-Source Mappings — Why Every Sentence Needs a Source** | 5D | ⭐ The excerpt as part of the mapping |
| 101 | Validation Loops and Hallucination Prevention | 4C | Checking output against evidence (checks 1–3) |
| 103 | Multi-Pass Review — When One Pass Is Not Enough | 4C | Check 4: an independent model checks support |
| Quiz 18, Quiz 14 | 5D and 4C quizzes | | Self-test |

**▶ Watch in this order (~20 min, est.):** #131 → #101 → #103, then do **Quiz 18** and **Quiz 14**.
(the mapping → validating against evidence → independent support check → self-tests)

### Udemy B: Claude Certified Architect Foundations: Complete Course (Voroniuk)
| # | Lecture | Section | Why |
|---|---|---|---|
| **67** | **Keeping Claims Tied to Sources** | S7 Multi-Agent | ⭐ Claim-source mapping with evidence |
| 92 | Independent Review | S9 CI | Check 4: an independent model checks support |
| 70 | Research Case: Citing Sources | S7 Multi-Agent | Exam-style case |
| Quiz 7 | Multi-Agent Systems quiz | S7 | Self-test |

**▶ Watch in this order (~15 min, est.):** #67 → #92 → #70, then do **Quiz 7**.

### CBT Nuggets: CCA-F course
| Skill | Why |
|---|---|
| 16. Improve Graders with Model Feedback | Check 4: model-based checks |
| 15. Evaluate Prompts with Test Datasets | Testing output quality against expected results |

*CBT is weak on D5. Use Udemy A or B for this topic.*
