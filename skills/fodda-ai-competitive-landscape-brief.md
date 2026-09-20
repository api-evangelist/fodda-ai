---
id: FODDA-UCS-COMPLANDSCAPE-001
name: fodda-competitive-landscape-brief
offering_key: skill.competitive_landscape
title: Fodda Competitive Landscape Brief
description: Produce a cited competitive landscape at the category/field level (several players, not one brand) - who's moving, on what trends, with what earnings signal and white space - composed from Fodda deep research + brand + earnings intelligence. Use for strategy reviews and pitch backgrounders that must be defensible.
version: 1.0.0
compliance: RFC-2119
owner: Fodda / PSFK
created: 2026-08-02
updated: 2026-08-02
---

# Fodda Competitive Landscape Brief

Drop this file into your agent and ask it to **"Run a Fodda Competitive Landscape Brief on
\<category or brand set\>."** Shared transport, auth, pricing, and Non-Negotiables:
**`references/fodda-api.md` (read first).**

## When to run (trigger)
Run when someone needs the **shape of a competitive field** - a category review, a market
entry read, a pitch backgrounder. This skill is **category-level**: it profiles a *field* of
several players with a research narrative, so it needs a category or ≥2 named brands in a Fodda
domain.

> **This vs Brand Intelligence:** for a **single brand's** profile - which already includes its
> ranked competitive set - use `fodda-brand-intelligence` (one call, ~$10). Use *this* when the
> unit of analysis is the **category/field**, not one brand. Both exist on purpose; agents and
> users pick by how they framed the question.

## The deliverable
A landscape brief: **executive read → the moving trends → player-by-player positioning →
earnings/pressure signal → white space & threats → sources.** Cited throughout; ranks players
by evidence, not vibes.

## How to build it (composition)
1. **Anchor research** - `POST /v1/research/deep-dive` `tier:"comprehensive"`
   (`deep_research_topic`) on the category. This is the narrative spine; poll to completion.
2. **Per-player footprint** - for each key brand, `POST /v1/brand-intelligence/{brand}`
   (`brand_tracker`); the canonical composition covers the top **3** named players - each
   additional player adds one `brand_intelligence` call (~$10).
3. **Earnings pressure** - for public players, `/v1/earnings/divergence` +
   `/v1/earnings/compare` (`get_earnings_divergence`) to spot who's under analyst pressure.
4. **White space** - `GET /v1/graphs/{graph_id}/adjacent` (`discover_adjacent_trends`) for
   territory no incumbent owns yet.
5. **Synthesize** under the Enrichment Firewall - a briefing, never a ranking fed into a
   decision system.

## Budget
**~$60 published** (canonical 3-player composition) = `deep_research` comprehensive $15 +
`brand_intelligence` ×3 $30 + `earnings_divergence` $7.50 + `adjacent_trends` $7.50. Each component
is metered individually, so actual runtime cost scales with the real player count via the live
402s - **state the player count and estimate to the requester before launching**, and offer a
`tier:"fast"` + fewer-players variant.

## Definition of done
- Player count and why-those-players stated up front (bounded scope, not silent truncation).
- Each player's position cites its brand-intelligence evidence.
- White-space claims name the adjacent trend and its source graph.
- `sources_cited > 0`; named-graph attribution throughout.

## Output contract (agent-to-agent)
```json
{ "category": "<string>", "executive_read": "<string>", "report_markdown": "<string>",
  "players": [{"name": "<string>", "position": "<string>"}], "white_space": ["<string>"],
  "sources_cited": <int>, "graphs_queried": ["<named graph>"] }
```
