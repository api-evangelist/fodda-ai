---
id: FODDA-UCS-TOPICBRIEF-001
name: fodda-topic-brief
offering_key: skill.topic_brief
title: Fodda Topic Brief
description: Answer a focused research question with a short, cited brief synthesized from Fodda's expert graphs - trends, the numbers behind them, and expert quotes. The fast, cheaper cousin of Deep Research: single-pass, graph-only, no live web. Use for a quick defensible answer, not a full report.
version: 1.0.0
compliance: RFC-2119
owner: Fodda / PSFK
created: 2026-08-02
updated: 2026-08-02
---

# Fodda Topic Brief

Drop this file into your agent and ask it to **"Give me a Fodda Topic Brief on \<question\>."**
Shared transport, auth, pricing, and Non-Negotiables: **`references/fodda-api.md` (read first).**

## When to run (trigger)
Run when the requester wants a **quick cited answer** to a focused question - the moving
trends, the key numbers, what experts say - without the cost or depth of a full report. The
topic MUST be in a Fodda domain.

> **This vs Deep Research:** Topic Brief is **single-pass, graph-only, ~$7.50** - fast and
> cheap. `fodda-deep-research` is **multi-pass, adds live web + institutional validation, and
> writes a full narrative report (~$10-15)**. Escalate to Deep Research when the answer needs
> to be comprehensive or board-ready.

## The deliverable
Unlike Brand Intelligence or Deep Research, `search_graph` returns **structured components,
not a finished report** - so this skill *synthesizes*: it queries the graphs, pulls the
supporting numbers and quotes, and writes a **short cited brief** (3-5 tight findings +
"so what"). The synthesis is the value the skill adds.

## How to build it (composition)
1. **Signals** - `POST /v1/graphs/{graph_id}/search` (`search_graph`) for the top trend
   clusters on the question; note `signal_score` (80+ strong, 60-79 moderate).
2. **Numbers** - `search_statistics` for the market sizes / growth rates behind the top signals.
3. **Voices** - `search_insights` for the expert quotes that frame them.
4. **Evidence** - `get_evidence` on the 2-3 load-bearing findings for citations.
5. **Synthesize** - write 3-5 findings ranked by signal + evidence, each cited, plus a
   one-line "so what." Not a raw result dump.

## Budget
≈ **15 calls ≈ $7.50** (the Topic Research offering). Read the exact price from the `402`.

## Definition of done
- 3-5 synthesized findings, each cited to a **named** graph/source - not a list of raw hits.
- Strongest findings lead (by `signal_score` + evidence count); weak signals flagged as such.
- `sources_cited > 0`. If the graphs are sparse on the topic, say so and suggest Deep Research.

## Output contract (agent-to-agent)
```json
{ "question": "<string>", "findings": [{"claim": "<string>", "signal_score": <int>, "source": "<citation>"}],
  "so_what": "<string>", "report_markdown": "<string>", "sources_cited": <int>,
  "graphs_queried": ["<named graph>"] }
```
