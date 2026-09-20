---
id: FODDA-UCS-EARNINGSDIV-001
name: fodda-earnings-divergence-read
offering_key: skill.earnings_divergence
title: Fodda Earnings Divergence Read
description: For a public company, surface where management's earnings-call narrative diverges from what analysts pressed on - deflections, dodged questions, and tone gaps - cited to the transcript and Fodda's earnings intelligence. Use for investor-lens or competitive-signal reads, not investment advice.
version: 1.0.0
compliance: RFC-2119
owner: Fodda / PSFK
created: 2026-08-02
updated: 2026-08-02
---

# Fodda Earnings Divergence Read

Drop this file into your agent and ask it to **"Run a Fodda Earnings Divergence Read on
\<ticker\>."** Shared transport, auth, pricing, and Non-Negotiables:
**`references/fodda-api.md` (read first).**

## When to run (trigger)
Run when the requester wants to know **where a public company's story and its analyst pressure
part ways** on the latest call - competitive intelligence, narrative tracking, category read.
Needs a ticker with Fodda earnings coverage.

> This is an **analytical read of public disclosures, not investment advice.** The agent MUST
> NOT recommend buying/selling/holding any security; if asked, say it's not licensed to advise
> and stick to describing the divergence.

## The deliverable
A read: **the narrative management pushed → what analysts pressed on → the divergences
(deflections, non-answers, tone shifts) → what it signals for the category** - each divergence
tied to the specific Q&A exchange.

## How to build it (composition)
1. **Divergence core** - `/v1/earnings/divergence` (`get_earnings_divergence`) for the ticker:
   the analyst-vs-management gaps.
2. **Q&A + deflections** - `/v1/earnings/company/{ticker}/qa` and `…/deflections`
   (`get_earnings_intelligence`) for the exchanges behind each gap.
3. **Category tie-in** - `search_graph` on the company's category so the read connects the
   divergence to broader moving signals.
4. **Synthesize** - describe, attribute to the transcript, and stop at analysis (see guardrail).

## Budget
**~$7.50** - this is the Earnings offering (`get_earnings_divergence` bills as `earnings_intelligence`,
15 calls), a single-capability deliverable. An optional category tie-in via `search_graph` adds a
separate `topic_research` call (~$7.50) if you run it. Read the exact price from each `402`.

## Definition of done
- Each divergence cites the specific Q&A exchange / transcript line.
- No investment recommendation anywhere in the output.
- Category tie-in cites a named graph.
- If coverage is thin for the ticker, say so rather than over-reading.

## Output contract (agent-to-agent)
```json
{ "ticker": "<string>", "management_narrative": "<string>", "analyst_focus": ["<string>"],
  "divergences": [{"topic": "<string>", "evidence": "<qa-ref>"}], "category_signal": "<string>",
  "report_markdown": "<string>", "sources_cited": <int> }
```
