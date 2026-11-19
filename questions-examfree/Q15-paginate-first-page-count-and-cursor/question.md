# Q15

*Source: CertyIQ practice set, Question 15.*

## Question

Your search products tool queries an external catalog API that returns paginated results (50 items per request). Production logs show queries frequently match 200+ products, and the design that auto-fetches all pages causes 15-20 second delays. How should you redesign the pagination handling?

- **A.** Create separate search products and fetch more results tools for pagination.
- **B.** Implement server-side relevance ranking and return only the top 50 most relevant items.
- **C.** Add a max pages parameter (default: 2) that controls how many pages are fetched internally.
- **D.** Return the first page with total match count and cursor for additional pages.

<details>
<summary><b>Show answer and explanation</b></summary>

**Topic:** Large result sets: return the first page, the total count and a cursor
**Domain / lesson:** D2 › 2.1 Tool Interface Design (closest lesson; the repo has no guide quote on pagination yet)
**Also touches:** D5 › 5.1 Context Window Management (don't flood the context)

## Answer: **D**

Return 50 items **fast**, plus two extra facts: "**212 matches in total**" and "**cursor for the next page**". Then the agent decides: often page 1 is enough; if not, it fetches more with the cursor, or narrows the search because 212 is too many.

| Option | Verdict | Why |
|---|---|---|
| **A** A separate `fetch_more_results` tool | ❌ | Adds a second tool for what is just a parameter of the same search. Without a total count, the agent also can't tell whether fetching more is worth it. |
| **B** Return only the top 50 by relevance | ❌ | Silently cuts the other 150+ matches. The agent can't get them even when it needs them, and doesn't know they exist. |
| **C** `max_pages` (default 2) fetched internally | ❌ | Still waits for 2 pages every time (about half the delay), and the rest are still cut off with no way to know about them. An arbitrary number instead of letting the agent decide. |
| **D** First page + total count + cursor | ✅ | Fast by default, nothing hidden (the count says how much exists), and the agent can get more only when it needs it. |

> Same idea as Q12/Q16 in this folder: **don't silently hide information** (B, C). Give the agent what it needs to make the call itself (D).

## Where it comes from

**Anthropic engineering blog, "Writing effective tools for AI agents"** (Sep 2025, read 2026-11-19):
- *"We suggest implementing some combination of pagination, range selection, filtering, and/or truncation with sensible default parameter values for any tool responses that could use up lots of context."* (**D**)
- *"For Claude Code, we restrict tool responses to 25,000 tokens by default."*
- *"If you choose to truncate responses, be sure to steer agents with helpful instructions."* (**D**: the count and cursor are that steer; **B** and **C** cut without telling)
- *"You can directly encourage agents to pursue more token-efficient strategies, like making many small and targeted searches instead of a single, broad search for a knowledge retrieval task."* (**D**: a big total count invites a narrower search)
- *"…tool implementations should take care to return only high signal information back to agents."*

**Guide:** no quote on pagination is recorded in this repo yet. The current guide site (aicertguides.com) couldn't be fetched in this session.

</details>
