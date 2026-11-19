# Q23

*Source: practice set (pasted text).*

## Question

Your agent has a log_workout tool that accepts exercise_type (string), value (number), and measurement (string). Production monitoring shows the agent frequently passes mismatched combinations, using measurement: "reps" for cardio exercises like running, or measurement: "miles" for strength exercises like bench press. Your exercises naturally divide into two categories: cardio (measured in time or distance) and strength (measured in reps and sets). 23% of tool calls have invalid combinations. What approach would most effectively reduce these errors?

- **A.** Implement server-side validation returning descriptive errors for invalid combinations, allowing the agent to retry with corrections.
- **B.** Add enum constraints on measurement limiting values to "minutes", "miles", "reps", or "sets" to prevent arbitrary measurement strings.
- **C.** Add explicit examples to the tool description showing valid combinations (e.g., "For running: use minutes or miles. For push-ups: use reps") with constraints for each exercise category.
- **D.** Split into log_cardio_workout (with duration_minutes or distance_miles parameters) and log_strength_workout (with reps and sets parameters).

<details>
<summary><b>Show answer and explanation</b></summary>

**Topic:** Split the tool so a wrong combination can't even be written
**Domain / lesson:** D2 › 2.1 Tool Interface Design
**Same idea as:** [Q17](../../questions/Q17-split-unified-tool/question.md) in the main set

## Answer: **D**

With two tools, each one only **has** the right fields:

```text
log_cardio_workout(exercise, duration_minutes | distance_miles)
log_strength_workout(exercise, reps, sets)
```

"Running in reps" can no longer be written, because the cardio tool has no `reps` field. Picking the tool **is** picking the category.

| Option | Verdict | Why |
|---|---|---|
| **A** Server returns errors, agent retries | ❌ | Catches the mistake **after** the bad call. The 23% still happen, now with extra retry turns. A good safety net, but not the fix. |
| **B** Enum on `measurement` | ❌ | Stops made-up strings like "laps", but "reps" **is** in the list, so "running + reps" still passes. The mismatch is between two fields, and an enum checks one field. |
| **C** Examples of valid combinations in the description | ❌ | Still just a request; it lowers the error rate but doesn't remove it. |
| **D** Two tools, each with only its own fields | ✅ | The invalid combinations can't be expressed, so they can't happen. |

> **Make the wrong call impossible to write, rather than explaining how to avoid it.**

## Where it comes from

**Guide D2 › 2.1 Tool Interface Design** (as recorded in Q17):
- *"Generic tools with broad responsibilities create ambiguity, because a broad description fits many requests."* *"Split them into purpose-specific tools with defined input and output contracts."* (**D**)
- *"Few-shot examples add token overhead without addressing the root cause."* (**C**)
- Opposite trap, so don't over-apply it: *"Consolidating similar tools into one as the first step."*

</details>
