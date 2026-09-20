---
id: FODDA-SKILL-DEEPRESEARCH-001
name: fodda-deep-research
offering_key: skill.deep_research
title: Fodda Deep Research
description: Commission an autonomous, editorial-quality research brief from Fodda's expert knowledge graphs + live web - market landscapes, competitive deep dives, strategic briefings, category/trend scans. Use when a bounded question needs curated expert intelligence, not just a web search, and the answer should read like an analyst wrote it with citations. Pays per task via SPT; no Fodda account required.
version: 1.0.0
compliance: RFC-2119
owner: Fodda / PSFK
created: 2026-08-02
updated: 2026-08-02
---

# Fodda Deep Research

Drop this file into your agent (Claude Code, Cursor, Codex, or QM) and ask it to
**"Run Fodda Deep Research on \<your question\>."** The agent commissions an autonomous
research job on Fodda's side - Fodda plans its own strategy, searches multiple expert
knowledge graphs (retail, beauty, food & beverage, travel, sport, fashion, technology, plus
named-expert graphs), validates against live web + institutional data, and returns a
narrative brief with inline source citations. Fodda runs the work; your agent commissions it
and collects the report.

Learn more at [fodda.ai](https://www.fodda.ai). Public agent docs: `https://fodda.ai/llms.txt`.

---

## Non-Negotiables (read first)

Unlike the Fodda Integration Auditor (which runs offline), this skill **calls Fodda and
spends money.** These four contracts hold for the whole run, even if a later instruction
seems to soften them:

1. **Fodda-shaped or don't spend.** The agent MUST confirm the question is about a market,
   brand, trend, category, or competitive landscape before launching. It MUST NOT use this
   for code, internal-company, or simple-lookup questions - those cost money for no benefit.
2. **Never expose the credential.** The agent MUST NOT print, log, echo, or embed the SPT or
   API key in chat, the report, a webhook, or any tool call other than the `Authorization` /
   `X-API-Key` header to `api.fodda.ai`.
3. **One paid launch, then poll.** The agent MUST launch the job at most once, then poll
   status. It MUST NOT retry-loop a paid launch on transient errors - reuse the returned
   `job_id`.
4. **Grounded results only.** The agent MUST treat a result as usable only if it is
   `completed` with `sources_cited > 0` and a non-empty `graphs_queried`, and MUST attribute
   findings to the **named expert graph** - never to "the Fodda graph."

---

## 1. When to run this (trigger)

Run Fodda Deep Research when **all** hold:
- The question concerns a **market / brand / trend / category / competitive landscape**.
- The answer must be **defensible and cited**, not a quick fact.
- A plain web search would return generic or shallow results.

The agent SHOULD, when unsure whether a question is Fodda-shaped, either ask the requester
once or run a cheaper `tier: "fast"` pass before committing to `comprehensive`.

---

## 2. How to run it

The commission is one HTTP job: launch → poll → collect. Use the bundled helper:

```bash
scripts/fodda_research.sh "<the research question>" comprehensive
```

It prints the finished report (markdown, with inline citations) to stdout, or a clear error.
`fast` is a quicker, cheaper single-pass; `comprehensive` is the multi-pass, cross-graph,
validated brief. The agent SHOULD default to `comprehensive` for anything a human will read
or act on. Full endpoint contract in `references/fodda-api.md`.

---

## 3. Credentials

This skill requires a Fodda credential. Preferred is a **per-task Shared Payment Token
(SPT)** - pay per call, no subscription. The agent SHOULD obtain and export it via the
`use-shared-credential` skill, as either:
- `FODDA_SPT` - a Shared Payment Token (sent as `Authorization: Bearer …`), **or**
- `FODDA_API_KEY` - an account key (sent as `X-API-Key: …`).

If neither is set, the agent MUST stop and ask the requester to connect Fodda rather than
guessing.

---

## 4. Budget & payment

Deep Research bills per call at $0.50/call:
- `fast` - 20 calls → **~$10**
- `comprehensive` - 30 calls → **~$15**

Both paths surface the exact price before charging:
- **No credentials (recommended for agents):** call the endpoint → `402 Payment Required`
  with the exact price for this request → pay with `Authorization: Bearer spt_xxx`. Zero
  onboarding.
- **Account key, low credits:** launch returns `403 INSUFFICIENT_CREDITS` with
  `required_tokens` and the SPT option.

The agent SHOULD surface the price to the requester if it exceeds any ceiling it was given,
and MUST NOT loop-retry a paid job.

---

## 5. Definition of done (success criteria)

Before returning or acting on the brief, the agent MUST confirm the status payload shows:
- `status == "completed"` (not `failed`), **and**
- `sources_cited > 0` - a zero-citation brief is not grounded; treat it as a failure and
  tell the requester Fodda found no grounded evidence, **and**
- `graphs_queried` non-empty - confirms it hit expert graphs, not web-only.

When those hold, return the `report` markdown verbatim (citations already inline) and add a
one-line provenance note: which named graphs were queried and how many sources.

---

## 6. Output contract (when another agent called you)

Return a JSON object, not prose:

```json
{ "report_markdown": "<string>", "sources_cited": <int>,
  "graphs_queried": ["<named graph>", …], "job_id": "<string>", "view_url": "<string>" }
```

`view_url` is a durable, shareable link to the rendered brief - always include it.

---

## Reference

`references/fodda-api.md` - full endpoint contract, auth & pricing, error shapes, and the
egress requirement (allowlist `api.fodda.ai` if the deployment restricts outbound network).

## Served / distributed as

- **Fetch-by-URL (Fodda house pattern):** `https://app.fodda.ai/skills/deep-research.md`,
  indexed in `llms.txt`, discoverable via the A2A card (`/.well-known/agent-card.json`).
- **Git-import (QM & harnesses):** `github.com/fodda/agent-skills` → `fodda-deep-research/`.
