# Q33

*Source: practice set (pasted text).*

## Question

Your team is extracting structured data from 50,000 legacy legal contracts under a two-week deadline. Initial testing with 500 sample documents shows 82% pass JSON schema validation on the first attempt, while the remaining 18% fail due to diverse issues: missing required fields, malformed dates, and incorrectly identified parties. Documents that fail typically need refinements targeting their specific failure modes before extraction succeeds. Which batch processing strategy is the most cost-efficient while still meeting the deadline?

- **A.** Split documents into 10 sequential batches of 5,000 each, analysing results and refining prompts between batches to improve extraction quality progressively.
- **B.** Submit all 50,000 documents via batch API, then submit failed extractions in successive batches, refining prompts between each batch, until all documents pass validation.
- **C.** Use the real-time API for all 50,000 documents since the batch API's 24-hour processing window creates unacceptable deadline risk.
- **D.** Process 2,000 sample documents via real-time API to identify failure patterns and refine prompts, then batch process all 50,000 with the optimized prompts.

<details>
<summary><b>Show answer and explanation</b></summary>

**Topic:** Fix the prompt on a sample (fast, real-time), then batch everything once (cheap)
**Domain / lesson:** D4 › 4.5 Batch Processing
**Similar to:** [Q111](../../questions/Q111-batch-standard-realtime-urgent/question.md), [Q116](../../questions/Q116-batch-if-24h-feedback-still-actionable/question.md), [Q120](../../questions/Q120-batch-api-same-model-half-price/question.md) in the main set

## Answer: **D**

Use each API for what it's good at:

| Phase | API | Why |
|---|---|---|
| 1. Find and fix the failure patterns on 2,000 samples | **Real-time** | Each test round takes minutes, not up to 24 hours, so you can iterate many times in a day or two. |
| 2. Run all 50,000 with the improved prompt | **Batch** | Half price, and the 24-hour window fits easily into two weeks. The pass rate is now much higher than 82%. |

Only 2,000 documents (4%) pay full price. The other 50,000 run at half price, and far fewer fail.

| Option | Verdict | Why |
|---|---|---|
| **A** 10 sequential batches of 5,000, refine in between | ❌ | Every round can take up to 24 hours, so 10 rounds plus analysis can use the whole two weeks. The early batches still run with the weak prompt. |
| **B** Batch all 50,000 now, resubmit failures until all pass | ❌ | ~9,000 documents fail with the untested prompt, and each fix round can take 24 hours. "Until all pass" may never end: some failures can't be fixed by retrying (see [Q31](../Q31-retries-cant-fetch-missing-information/question.md)). |
| **C** Real-time for all 50,000 | ❌ | Twice the cost, for no reason. A 24-hour batch window is no deadline risk in a two-week project. |
| **D** Refine on 2,000 in real time, then batch all | ✅ | Fast learning where speed matters, and the cheap API for the bulk. |

**My note:** after the big batch, resubmit only the documents that still fail (matched by `custom_id`), and send the ones missing information to human review.

## Where it comes from

**Guide D4 › 4.5 Batch Processing** (as recorded in Q111, Q116, Q120):
- *"Use the synchronous API for blocking workflows and the batch API for latency-tolerant ones."* Prompt refinement is blocking (you wait for each result to decide the next change); the 50,000-document run isn't. (**D**)
- *"An overnight job loses nothing when it waits, and it costs half as much."* (**C**)
- *"Up to 24-hour processing window: results may arrive in minutes or take up to 24 hours."* *"Design around the 24-hour maximum."* (**A**, **B**: many batch rounds add up)
- *"each request in a batch gets a unique identifier used to match it with its response"* (`custom_id`, for resubmitting failures)

**From memory, not verified in this repo:** the official exam guide's 4.5 skills include refining prompts on a sample set before batch-processing large volumes, to raise the first-pass success rate and cut resubmission costs. (**D**)

</details>
