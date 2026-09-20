---
id: FODDA-UCS-TRENDWATCH-001
name: fodda-category-trend-watch
offering_key: skill.trend_watch
title: Fodda Category / Trend Watch
description: Stand up a recurring watch on a category - each run surfaces what's newly moving, what's fading, and one adjacent signal to watch, cited to Fodda's expert graphs and delivered to Slack/email. Use when a team wants an always-on category radar, not a one-off report.
version: 1.0.0
compliance: RFC-2119
owner: Fodda / PSFK
created: 2026-08-02
updated: 2026-08-02
---

# Fodda Category / Trend Watch

Drop this file into your agent and ask it to **"Set up a Fodda Trend Watch on \<category\>,
\<cadence\>."** Shared transport, auth, pricing, and Non-Negotiables:
**`references/fodda-api.md` (read first).**

## When to run (trigger)
Run when the requester wants an **ongoing radar** on a category - a weekly/biweekly digest -
rather than a single brief. Needs a category in a Fodda domain and a cadence. This skill both
**sets up** the schedule and **executes each run**.

> ⚠️ Recurring cost. Setting up a schedule commits the requester to a **per-run charge**. The
> agent MUST state the per-run estimate and cadence and get explicit approval before creating
> the schedule (this is a standing configuration - treat like any recurring commitment).

## The deliverable
Per run: a short digest - **↑ newly rising · ↓ fading · 1 adjacent signal to watch** - each
line cited, plus a one-line "so what." Delivered to the configured Slack channel / inbox.

## How to build it (composition)
1. **Baseline (once)** - `search_graph` on the category to record the current top signals as
   the comparison baseline.
2. **Create the schedule** - `manage_scheduled_reports` / `POST /v1/research/schedules` with
   category, cadence, and destination. Confirm cost first (see warning above).
3. **Each run** - `POST /v1/graphs/{graph_id}/search` for the current signal set; diff against
   the last baseline to find rising/fading; `GET /v1/graphs/{graph_id}/adjacent` for one
   watch-item; update the baseline.
4. **Deliver** the digest to the destination; attribute every line to its named graph.

## Budget
Setup ≈ topic(15). **Each run** ≈ topic(15) + adjacent(15) ≈ **30 calls ≈ $15/run** -
recurring. Make the run cost and cadence explicit and get approval before scheduling.

## Definition of done
- Requester approved the recurring per-run cost and cadence before the schedule was created.
- Each digest shows real deltas vs the prior baseline (not a static re-list).
- Every rising/fading/watch line cites a named graph.
- A visible "stop / change cadence" instruction ships with the first digest.

## Output contract (per run)
```json
{ "category": "<string>", "run_date": "<iso>", "rising": ["<string>"], "fading": ["<string>"],
  "watch": "<string>", "so_what": "<string>", "sources_cited": <int>,
  "graphs_queried": ["<named graph>"], "schedule_id": "<string>" }
```
