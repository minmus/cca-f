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

## Check question 2: does "pass both in full" make option A right?

**Lesson 1.3 says the coordinator "must pass both in full". In one sentence, why doesn't that make option A (pass the full accumulated context) correct?**
*My answer:* "Because that would overwhelm the report generator." 🟡 Partial. Right about the **consequence**, but it doesn't resolve the conflict with the guide's wording.

**The missing piece: "in full" is about findings, not raw material.**
- A **finding** = content + its metadata. Lesson 1.3, Rule 2: *"Content is the claim, the fact or the analysis."* *"Metadata says where the content came from: source URL, document name, page number."*
- **B already passes the findings in full:** every claim (in the draft) together with its source (in the index).
- **A adds the 120K of raw pages.** Those aren't findings. They're what the search agent *read* in order to produce findings.

**The reasoning order the exam rewards:**
1. B already satisfies "complete".
2. Everything A adds on top is non-findings.
3. Those extras cost something. This is where "overwhelm" comes in.

Your answer is step 3. Steps 1–2 come first, because "it's too much" alone doesn't beat a guide rule that literally says "in full".

**"Overwhelm", made precise (D5 › 5.1):**
- **Lost in the middle:** *"A model may miss findings in the middle of a long context, or may give them less weight."*
- **Cost and latency:** ~138K tokens per call instead of a few K.
- **Drift:** with all the raw material in front of it, the report generator may re-synthesize and depart from the approved draft.
- A bigger context window doesn't fix any of these. The exam is model-agnostic.

> **Rule:** "Complete" means every finding with its metadata. It doesn't mean every token anyone read.

## Check question 3: is the draft alone "complete"?

**An exam option says: "Pass only the 3K synthesis draft, since it's the most compact complete version of the findings." Is that "complete" in lesson 1.3's sense? Why or why not?**
*My answer:* "That is complete, as a subagent receives only what the coordinator writes into the subagent's prompt." ❌ Not this time. The quote is right, but it points the **other way**.

**Why the draft alone is not complete:**
- A finding = **content + metadata** (check question 2). The draft keeps the content (the claims) but has **dropped the metadata** (URLs, excerpts). That's half of each finding.
- The quote you used is exactly why that's fatal. The report generator gets **only** what's in its prompt, so if no sources are in the prompt, it has no way to cite. Lesson 1.3: *"A downstream agent can attribute a claim to a source only if the coordinator passes that metadata with the claim."*
- This is Q02's option **C** without the post-processing. Lesson 1.3's exam expectation: *"When a synthesis agent produces unsourced claims, the exam expects you to identify the context passing failure: missing structured metadata."*

**The distinction to keep:**
- *"A subagent receives only what the coordinator writes into its prompt"* describes what the subagent **has**.
- "Complete" is about what it **needs** to do its job.

The quote is a **warning** that whatever you leave out is gone. It isn't a definition of complete.

**Compact ≠ complete.** There are two ways to fail:

| Option | Content (claims) | Metadata (sources) | Extra (raw pages) | Complete? | Efficient? |
|---|---|---|---|---|---|
| A: full context | ✅ | ✅ | ❌ 120K | ✅ | ❌ too much |
| Draft only | ✅ | ❌ | none | ❌ too little | ✅ |
| **B: draft + source index** | ✅ | ✅ | none | ✅ | ✅ |

## Check question 4: draft + a plain list of URLs

**The coordinator passes the report generator the 3K draft plus a plain list of all 25 source URLs, with no mapping between claims and sources. Is that complete? Why or why not?**
*My answer:* "No, because of the same reason." 🟡 Partial. The verdict is right, but the reason is **related, not the same**.

**What's different:**
- **Draft only:** the metadata is **missing**. Fix: add the sources.
- **Draft + URL list:** the metadata is **present but not linked** to claims. Fix: add the **mapping**.

With 25 URLs and dozens of claims, the report generator has to guess which URL backs which claim. That's the same guesswork as Q02's option C, just moved inside the generator, and it produces the **unsupported citations** from check question 1. A plain list is a bibliography, not citations.

**From the guide:**
- Lesson 1.3: *"A downstream agent can attribute a claim to a source only if the coordinator passes that metadata **with the claim**."*
- Lesson 5.6: *"A claim-source mapping links one finding to its source."*

The structure that matters is the **link**, not just the list.

| Option | Content (claims) | Metadata (sources) | Claim → source link | Extra (raw pages) | Complete? | Efficient? |
|---|---|---|---|---|---|---|
| A: full context | ✅ | ✅ | buried in 138K | ❌ 120K | ✅ | ❌ too much |
| Draft only | ✅ | ❌ | ❌ | none | ❌ too little | ✅ |
| Draft + URL list | ✅ | ✅ | ❌ | none | ❌ unlinked | ✅ |
| **B: draft + source index** | ✅ | ✅ | ✅ | none | ✅ | ✅ |

## Check question 5: wrap-up

**In one line, what three things must a complete handoff to the report generator contain?**
*My answer:* "Draft + sources plus their index." ✅ All three are there.

| Your words | Part | What it gives the report generator |
|---|---|---|
| draft | **Content** | The narrative and its claims |
| sources | **Metadata** | URL + excerpt for each source |
| their index | **Link** | Which source backs which claim |

**One precision:** the index has to be keyed **by claim** (claim → sources), not by source. A list of sources is just the bibliography from check question 4. Each entry needs to answer "where does *this claim* come from?"

> **Q02 in one line:** a complete handoff = **content + metadata + the claim → source link**, and nothing else.

## Open check question

**Which Q02 option has the same shape as Q01's option A ("have the report generator query the web search agent to re-locate sources")?**
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
