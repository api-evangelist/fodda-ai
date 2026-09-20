---
id: FODDA-UCS-FITDISCOVERY-001
name: fodda-fit-discovery
offering_key: skill.fit_discovery
title: Fodda Fit Discovery (Integration Auditor)
description: Analyze a codebase offline and produce a report of where Fodda's expert intelligence would add value - and, honestly, where it wouldn't. The adoption/discovery use case. Runs entirely local; makes no Fodda calls.
version: 2.4.0
compliance: RFC-2119
owner: Fodda / PSFK
created: 2026-06-08
updated: 2026-08-02
---

# Fodda Fit Discovery

This is the **discovery** Use Case Skill - the top-of-funnel counterpart to the execution
skills in this catalog. Unlike the others, it **runs offline and makes no Fodda calls**; it
maps opportunities inside a prospect's codebase.

The canonical, always-current version is the **Fodda Integration Auditor**, served at:

- `https://app.fodda.ai/Fodda_Integration_Auditor.md`

Drop that file into an IDE agent (Cursor, Windsurf, Claude Code) and prompt **"Run the Fodda
Use Case Discovery."** This entry exists so the discovery skill appears in the catalog
alongside the execution skills; **do not fork the Auditor here** - fetch the canonical copy so
version, numbers, and the Core-Four privacy contract stay current.

Distinct from the execution skills:
- **No spend, no credential** - it never calls Fodda; the only network fetch is this spec.
- **Output is a human report** (for PMs/engineers), not an agent-to-agent JSON contract.
- It *recommends* the execution skills in this catalog as the integration paths it finds.
