---
id: FODDA-UCS-BRANDINTEL-001
name: fodda-brand-intelligence
offering_key: skill.brand_intelligence
title: Fodda Brand Intelligence
description: Pull a complete Brand Intelligence Profile for one brand in a single call - trend footprint, ranked co-occurring brands, cross-graph presence, evidence timeline, and lifecycle - from Fodda's expert graphs. Use for a fast, cited read on how one brand shows up across the intelligence.
version: 1.0.0
compliance: RFC-2119
owner: Fodda / PSFK
created: 2026-08-02
updated: 2026-08-02
---

# Fodda Brand Intelligence

Drop this file into your agent and ask it to **"Pull Fodda Brand Intelligence on \<brand\>."**
Shared transport, auth, pricing, and Non-Negotiables: **`references/fodda-api.md` (read first).**

## When to run (trigger)
Run when the requester wants the picture for **one named brand** - how it shows up across the
graphs, which trends it rides, who it co-occurs with. The brand MUST be in a Fodda domain.

> **This vs Competitive Landscape Brief:** use *this* for a **single brand's** profile (one
> call, ~$10). For the **shape of a whole category / several players** with a research
> narrative, use `fodda-competitive-landscape-brief` (composed, pricier). Agents/users may
> reach for either - pick by whether the subject is one brand or a field.

## The deliverable
`brand_tracker` returns a **complete Brand Intelligence Profile in one call** - trend
footprint, competitive landscape (co-occurring brands ranked by overlap), cross-graph
presence, evidence timeline, and lifecycle distribution. This is a finished product; the
skill's job is to fire it correctly, pay, and **present the structured profile as a readable,
cited brief** - not to re-assemble it from parts.

## How to build it (composition)
One call - no composition needed:
1. `POST /v1/brand-intelligence/{brand}` (`brand_tracker`). That's the deliverable.
2. Optionally `get_evidence` on the 2-3 load-bearing trends if the requester wants the
   source detail expanded.

## Budget
≈ **20 calls ≈ $10** (the Brand Intelligence offering). Read the exact price from the `402`.

## Definition of done
- Profile returned with a ranked set of co-occurring brands and `sources_cited > 0`.
- Presented as a readable brief, attributed to **named** graphs/experts - not "the Fodda graph."
- If the brand has no meaningful footprint, say so plainly rather than padding.

## Output contract (agent-to-agent)
```json
{ "brand": "<string>", "trend_footprint": ["<trend>"], "co_occurring_brands": ["<brand>"],
  "lifecycle": "<summary>", "report_markdown": "<string>", "sources_cited": <int>,
  "graphs_queried": ["<named graph>"] }
```
