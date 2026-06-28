# Worked Example — Northwind Forge

A condensed, end-to-end application of the playbook to a fictional client, to show the *output*, not just the method. Northwind Forge is invented; any resemblance to a real company is coincidental.

> **Client (fictional):** Northwind Forge, a 55-year-old Canadian manufacturer of precision metal components. Roughly 300 staff. Decades of engineering drawings, process sheets, quality records, supplier correspondence, and troubleshooting know-how, most of it on file shares and in long-tenured employees' heads. Wants a private, AI-powered knowledge base to support engineering, manufacturing, troubleshooting, and quality, and eventually to fold in real-time machine data. Explicitly does not want to start building yet.

---

## Phase 0 — Assessment findings

**Knowledge-source inventory (summary).**

| Source | Volume | Format | Source of truth? | State |
|---|---|---|---|---|
| Engineering drawings | ~40k | scanned PDF + native CAD | yes (for geometry) | mostly scanned, no metadata |
| Process sheets | ~8k | Word + PDF | partially | duplicated across shares |
| Quality records | ~120k | PDF + spreadsheets | yes (for compliance) | well-structured, poorly tagged |
| Troubleshooting notes | scattered | email, notes, tribal | no | largely uncaptured |
| Supplier correspondence | large | email + PDF | no | mixed sensitivity |

**Readiness scan (via `litmus`), per intended use case.** The same corpus scored very differently by goal:

- `troubleshooting-assistant`: **low readiness.** The knowledge that matters most (troubleshooting know-how) is the least captured; recency and equipment linkage are weak.
- `engineering-spec-lookup`: **medium.** Drawings exist but are scanned images with no metadata; OCR and equipment tagging are the blockers.
- `qa-compliance-retrieval`: **medium-high.** Quality records are structured and authoritative; the gap is tagging and controlled-copy discipline.

**Headline remediation backlog (prioritized):** OCR and tag the engineering drawings; capture troubleshooting knowledge through a structured intake; deduplicate process sheets and designate source-of-truth; backfill core metadata on quality records.

The single most important finding: the highest-value use case (troubleshooting) is the *least* ready, because that knowledge lives in people, not documents. That reframes the early roadmap from "index what we have" to "capture what we know."

---

## Information architecture recommendation

- **Ontology:** Equipment, Process, Product, Material, FailureMode, Procedure, Supplier, and the relationships among them (a procedure *applies to* equipment; a failure mode *occurs on* equipment; a drawing *describes* a product).
- **Metadata schema:** the playbook core set, with `equipment_id` and `process_id` as the load-bearing fields, because almost every real question is anchored to a machine or a process.
- **Source-of-truth model:** drawings authoritative for geometry, quality records authoritative for compliance, a new curated store authoritative for troubleshooting.
- **Document standard + intake:** a one-page troubleshooting-capture template so tribal knowledge enters structured and equipment-linked from day one.

---

## Architecture and platform recommendation

**Recommendation:** start managed on the cloud Northwind already uses for back-office (AWS), using Bedrock Knowledge Bases for the first use case, with private networking, and S3 as the document source. Keep retrieval hybrid (semantic plus keyword, because part numbers and spec codes must match exactly). Add an equipment/process knowledge graph in the walk phase for relational troubleshooting questions, and plan a structured path for machine data in the run phase.

**Why, and the alternatives considered:**

- *Managed (Bedrock KB) over assembled:* Northwind has no ML platform team; operating a vector store and pipeline is undifferentiated work for them. Managed removes it.
- *AWS over Azure:* their existing cloud and identity already sit there; the integration advantage outweighs model differences. (If they were a Microsoft 365 shop, the recommendation flips to Azure AI Search plus Azure OpenAI.)
- *Private networking, not public endpoints:* their drawings are core IP; this is a hard requirement that shapes the whole design, and a self-hostable fallback (such as Onyx) is kept on the table if compliance later demands fully air-gapped.
- *Hybrid retrieval, not pure vector:* spec codes and part numbers are exact-match tokens that embeddings blur.

**Governance (the `steward` layer):** classification at ingest (drawings and supplier correspondence carry sensitivity), permission-aware retrieval so shop-floor and engineering see different scopes, provenance on every answer, and an audit log. This is called out as the highest-risk area to get wrong.

---

## Roadmap

- **Crawl (0 to 3 months):** finish assessment, agree IA and standards, define governance, begin drawing OCR and troubleshooting capture, pilot the QA-compliance use case on Bedrock KB with evaluation in place (it is the readiest, so it ships value fastest and validates the architecture).
- **Walk (3 to 9 months):** expand to engineering-spec lookup once drawings are tagged, enforce permission-aware retrieval across scopes, add the equipment/process graph for troubleshooting, stand up observability and a feedback loop.
- **Run (9+ months):** continuous freshness, online evaluation and drift monitoring, cost governance, integrate first machine-data feeds joined to the equipment ontology, hand operational ownership to the internal team.

---

## Mistakes called out for this client

- Do not start with engineering-spec lookup just because the drawings are the biggest pile; they are the least ready (scanned, untagged). Start with quality records.
- Do not index troubleshooting email as-is; capture it structured, or the most valuable use case stays unanswerable.
- Do not defer permission scoping; shop-floor and engineering must see different things from day one.
- Do not skip the equipment ontology; without it, "what procedures touch this machine" stays unanswerable no matter how good the embeddings are.

---

## What this demonstrates

The value here was not a model or a vendor. It was sequencing: assess first, find that the highest-value knowledge is the least captured, and let that reorder the roadmap. That is the judgment the engagement is buying.
