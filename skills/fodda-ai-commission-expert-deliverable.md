---
id: FODDA-UCS-EXPERTDELIVERABLE-001
name: fodda-commission-expert-deliverable
offering_key: skill.commission_expert_deliverable
title: Fodda Commission Expert Deliverable
description: Commission a named Fodda expert (a Digital Twin analyst) to produce a packaged deliverable from their own knowledge graph, chosen from their listed commissionable offerings. Use when you want an expert's finished work product (a report, teardown, or analysis they offer), not a live data pull. Account plus credits required; anonymous SPT agents cannot commission this rail yet.
version: 1.0.0
compliance: RFC-2119
owner: Fodda / PSFK
created: 2026-08-02
updated: 2026-08-02
---

# Fodda Commission Expert Deliverable

Drop this file into your agent and ask it to **"Commission \<expert\>'s \<deliverable\>."** This
is the bridge to Fodda's **expert** offerings: a named Digital Twin analyst produces a packaged
deliverable from their curated graph. Shared transport, auth, and non-negotiables:
**`references/fodda-api.md` (read first)** - but note this rail differs from the platform skills
(see Credentials & payment).

## This is a different rail from the platform skills

- **Platform skills** (`skill.deep_research`, `skill.brand_intelligence`, ...) run a *capability*,
  and an anonymous agent can pay per call via SPT/402.
- **This skill** commissions an *expert offering* (`kind: "offering"`) via `request_deliverable`.
  It is **account + credits only; there is no 402/SPT.** An anonymous SPT agent cannot use this
  path yet and will get `401 AUTH_REQUIRED`.

For a conversational expert *opinion* (per turn, SPT-capable), use `fodda-synthetic-consultant`
instead. Use *this* when you want a commissioned, packaged work product.

## Non-Negotiables (read first)

1. **Account required.** This rail needs a raw API key (`X-API-Key`). If the caller is anonymous
   or SPT-only, stop and tell the requester an account is needed; do not retry as anonymous.
2. **Never send an empty `brief`.** `brief` is required and shapes the deliverable.
3. **Price is charged up front from credits.** Surface the offering's `published_price_usd` to the
   requester if it exceeds any ceiling they set, before commissioning.
4. **One commission, then poll.** Never re-fire a commission on a transient error; reuse `job_id`.
5. **Attribute to the named expert.**

## 1. When to run (trigger)
Run when the requester wants a **named expert's finished, packaged deliverable** from that
expert's commissionable offerings, and the target is an active **Digital Twin** analyst. Not for
live data pulls (use a platform skill) or a quick opinion (use `fodda-synthetic-consultant`).

## 2. How to run it
1. **Discover** the analyst and offering: `list_analysts`, or read the OKF expert card's
   "Commissionable deliverables". Pick the `analyst_id` and a live `offering_key`.
2. **Commission**: `POST /v1/analysts/{analyst_id}/deliver` with body
   `{ "offering_key": "...", "brief": "<non-empty>", "attachments": [{ "content": "..." }] }`
   (attachments optional, max 5). Via MCP, call `request_deliverable` and pass `brief`.
   Returns `202 { ok, job_id, status: "working", poll }`.
3. **Poll** `GET /v1/analysts/deliverables/{job_id}` until `status` is `completed` or `failed`
   (`working` = keep polling). The deliverable body is the expert's final message in the
   completed response (not a sandbox file).

## 3. Credentials & payment
- **Account + credits only.** No 402, no SPT on this rail.
- Requires a raw API key. OIDC / MCP-brokered identities are refused with `400 NO_BILLABLE_KEY`;
  no account gives `401 AUTH_REQUIRED`.
- Charges the offering's `published_price_usd` up front via account credits. Internal reads the
  expert runs are marked `x-fodda-billing: mcp-orchestrated`, so you are not double-billed.

## 4. Error handling (grounded in the contract)
- `422 NOT_DELIVERABLE_CAPABLE` - analyst is inactive or not a Digital Twin. Pick a Digital-Twin analyst.
- `404 OFFERING_NOT_FOUND` - bad or inactive `offering_key`. Re-discover via `list_analysts`.
- `403 OFFERING_NOT_OWNED` - offering not owned by that analyst (note: ownership is not currently enforced; the call is allowed and logged).
- `403` on poll - cross-account job; you can only poll your own commissions.
- `503 AGENT_DISABLED` - kill-switch is on; stop and report.
- `401 AUTH_REQUIRED` / `400 NO_BILLABLE_KEY` - see Credentials & payment.

## 5. Definition of done
- `status == "completed"` (not `failed`) and the deliverable body is present.
- Attributed to the named expert.
- Price was surfaced to the requester if it exceeded their ceiling.

## 6. Output contract (agent-to-agent)
```json
{ "analyst_id": "<string>", "offering_key": "<string>", "deliverable_markdown": "<string>",
  "job_id": "<string>", "status": "completed|failed", "price_usd": <number> }
```

## Budget
Equals the commissioned offering's `published_price_usd` (varies by expert and offering), charged
up front from account credits. There is no per-call 402 on this rail.
