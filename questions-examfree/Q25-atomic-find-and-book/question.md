# Q25

*Source: practice set (pasted text). A few words were cut off in the paste; they're filled in here in [brackets].*

## Question

Your scheduling agent uses get_available_slots(date, provider_id) to retrieve open appointment times, then book_appointment(provider_id, slot_time, patient_id) to reserve a slot. [Support] tickets show that 15% of booking attempts fail with "slot no longer available" because another user booked the slot between the availability check and the booking call. How should you r[edesign] these tools?

- **A.** Modify book_appointment to return detailed failure information including currently available alternative slots when the requested slot is unavailable, enabling the agent to retry with a di[fferent] time.
- **B.** Keep both tools but add retry logic to the agent's system prompt, instructing it to call get_available_slots again and select a different time if booking fails.
- **C.** Add a hold_slot(provider_id, slot_time) tool that creates a 60 second temporary reservation, requiring the agent to call it between checking availability and booking.
- **D.** Combine both tools into a single find_and_book_appointment that atomically checks availability and books, returning either the confirmed booking or available alternatives.

<details>
<summary><b>Show answer and explanation</b></summary>

**Topic:** Check-then-book race: make checking and booking one atomic step
**Domain / lesson:** D2 › 2.1 Tool Interface Design (tool composition)
**Similar to:** [Q29](../../questions/Q29-compose-mechanical-keep-judgment/question.md) in the main set (combine the mechanical steps)

## Answer: **D**

The bug is the **gap** between two separate calls:

```text
Agent: get_available_slots → "10:00 is free"
                                     ← another user books 10:00
Agent: book_appointment(10:00)  → "slot no longer available" ❌
```

Anything that keeps two separate steps leaves that gap open. D closes it: one tool checks **and** books in a single step on the server, so nobody can take the slot in between. If the slot is already gone, the same call returns alternatives.

| Option | Verdict | Why |
|---|---|---|
| **A** Failure returns alternatives | ❌ | Makes the retry quicker but doesn't stop the failure. The retry can hit the same race again. |
| **B** Prompt: re-check and retry on failure | ❌ | Same as A, but slower and only an instruction. The 15% failures still happen. |
| **C** `hold_slot` for 60 s in between | ❌ | Moves the gap (someone can still book between the check and the hold), adds a third call, and relies on the agent remembering to call it. Holds can also block slots for real users. |
| **D** One atomic check-and-book | ✅ | No gap, so no race. And when the slot is gone, it returns alternatives (A's benefit) in the same call. |

**Why this doesn't remove judgment:** the agent (with the user) still picks which time to ask for. Only the mechanical "is it free → book it" is combined, as in main-set Q29.

"Atomic" means the check and the booking happen as one indivisible step, so nothing can happen between them. This is a general software rule, not a guide quote.

## Where it comes from

**Anthropic engineering blog, "Writing effective tools for AI agents"** (read 2026-11-19):
- *"Tools can consolidate functionality, handling potentially multiple discrete operations (or API calls) under the hood."* (**D**)
- *"…tools can enrich tool responses with related metadata or handle frequently chained, multi-step tasks in a single tool call."* (**D**)
- *"Instead of implementing a `list_users`, `list_events`, and `create_event` tools, consider implementing a `schedule_event` tool which finds availability and schedules an event."* (**D**: almost the same example)

**Guide D2 › 2.1 Tool Interface Design** (as recorded in Q29): tools should be *"purpose-specific… with defined input and output contracts."* `find_and_book_appointment` has one purpose: book a slot or say what's free instead.

**Guide D1 › 1.4** (as recorded in Q11 in this folder, Q28): *"Prompt-based guidance is probabilistic. Programmatic enforcement is deterministic."* (**B**, **C**: they rely on the agent following instructions)

</details>
