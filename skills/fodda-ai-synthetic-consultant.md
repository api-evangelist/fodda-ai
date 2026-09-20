---
id: FODDA-UCS-CONSULT-001
name: fodda-synthetic-consultant
offering_key: skill.expert_consult
title: Fodda Synthetic Consultant
description: Consult a named synthetic expert (a Fodda Human Agent - e.g. Piers Fawkes, Ben Dietz) grounded in that expert's own knowledge graph. Routes the question to the right expert, gets an attributed opinion with itemized sources, and follows cross-lane referrals when a question leaves the expert's domain. Use for "what would an expert say about X."
version: 1.0.0
compliance: RFC-2119
owner: Fodda / PSFK
created: 2026-08-02
updated: 2026-08-02
---

# Fodda Synthetic Consultant

Drop this file into your agent and ask it to **"Consult a Fodda expert about \<question\>."**
This is agent-to-expert-agent: your agent delegates to a *named* synthetic expert grounded in
that expert's curated graph. Shared transport, auth, pricing, and Non-Negotiables:
**`references/fodda-api.md` (read first).**

## When to run (trigger)
Run when the requester wants a **named expert's point of view** - an opinion, interpretation,
or "how would \<domain\> read this," not a raw data pull. For a data/report deliverable use
`fodda-brand-intelligence` or `fodda-competitive-landscape-brief` instead.

## The deliverable
An attributed expert opinion: **the expert's take → their reasoning → itemized sources from
their graph → (if the question crossed lanes) the colleague they referred you to.** Always
named - "Piers Fawkes's Retail Agent says…", never "Fodda says."

## How to build it (composition)
1. **Pick the expert** - `GET /v1/analysts` (`list_analysts`) and match the question's domain
   to the right expert's lane. If several fit, prefer the most specific.
2. **Consult** - `POST /v1/analysts/consult` (`consult_analyst`) with the question and the
   chosen expert. This is conversational: the agent MAY run follow-up turns to sharpen, each
   turn billed.
3. **Honor referrals** - a Fodda expert **declines questions outside its lane and refers you to
   a colleague's agent.** The agent MUST follow one such referral hop when it happens, re-run
   the consult with the referred expert, and attribute both.
4. **Attribute** - return the opinion with the expert's name and their itemized sources; do not
   blend multiple experts into an anonymous voice.

## Budget
≈ **5 calls / $2.50 per consult turn**; a referral hop is another consult. Multi-turn consults
add up - the agent SHOULD cap turns (default 2-3) unless the requester asks to go deeper, and
surface running cost.

## Definition of done
- The opinion is attributed to a **named** expert (and any referred colleague), never to Fodda
  generically.
- Itemized sources from the expert's graph are included.
- A cross-lane referral, if it occurred, was followed once and both experts are credited.
- If no expert covers the domain, say so and stop - do not fabricate an expert or an opinion.

## Output contract (agent-to-agent)
```json
{ "question": "<string>", "expert": "<name>", "opinion": "<string>", "reasoning": "<string>",
  "sources": ["<citation>"], "referred_to": "<name|null>", "turns": <int> }
```
