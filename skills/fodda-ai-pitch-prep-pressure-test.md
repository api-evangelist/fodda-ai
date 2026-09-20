---
id: FODDA-UCS-PITCHPREP-001
name: fodda-pitch-prep
offering_key: skill.pitch_prep
title: Fodda Pitch Prep & Brief Pressure-Test
description: Take a client brief, competitor page, or draft strategy (URL or text) and pressure-test it against Fodda's expert graphs - validating claims, surfacing missed trends, adding cited evidence, and mapping the opportunity as a visual. Use before an agency pitch or strategy review.
version: 1.0.0
compliance: RFC-2119
owner: Fodda / PSFK
created: 2026-08-02
updated: 2026-08-02
---

# Fodda Pitch Prep & Brief Pressure-Test

Drop this file into your agent and ask it to **"Pressure-test this brief with Fodda: \<url or
text\>."** Shared transport, auth, pricing, and Non-Negotiables:
**`references/fodda-api.md` (read first).**

## When to run (trigger)
Run when the requester has a **draft artifact** - client brief, competitor teardown, pitch
narrative - and wants it hardened before it goes out. The subject MUST be in a Fodda domain.
Input is a URL or pasted text; if neither is given, ask for it.

## The deliverable
A red-team memo on the brief: **what holds up (with citations) → what's unsupported or stale →
trends the brief missed → 2-3 sharper angles → an opportunity map visual.** It strengthens the
requester's own argument; it does not write their pitch for them.

## How to build it (composition)
1. **Ingest** - `read_url` (or accept pasted text) to extract the brief's claims and framing.
2. **Validate claims** - for each load-bearing claim, `POST /v1/graphs/{graph_id}/search`
   (`search_statistics` / `search_insights`) + `get_evidence`: supported, contradicted, or
   unfound? Flag stale or single-source assertions.
3. **Find the gaps** - `search_graph` on the brief's topic for high-signal trends it omits;
   `GET /v1/graphs/{graph_id}/adjacent` for a non-obvious angle.
4. **Visualize** - `generate_visual` to map the validated opportunity (trend → evidence →
   move).
5. **Assemble** the memo under the Enrichment Firewall - advisory pressure-test, never a
   gate on the human's decision.

## Budget
**~$15** = `topic_research` $7.50 + `adjacent_trends` $7.50. (`read_url`, `get_evidence`, and
`generate_visual` are bundled utilities, not separately priced offerings.) Read exact prices from
each `402`.

## Definition of done
- Every "holds up" / "contradicted" verdict cites the specific graph or source.
- At least one genuinely missed trend surfaced (or an explicit "brief is well-covered").
- The visual reflects only validated points - no decoration of unsupported claims.
- Never embeds copy-pasteable source snippets; references by graph/source only.

## Output contract (agent-to-agent)
```json
{ "input_ref": "<url|text-id>", "supported": ["<claim>"], "unsupported": ["<claim>"],
  "missed_trends": ["<string>"], "sharper_angles": ["<string>"],
  "visual_url": "<string|null>", "sources_cited": <int>, "graphs_queried": ["<named graph>"] }
```
