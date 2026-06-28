# Enterprise Knowledge Architecture Playbook

A repeatable method for turning decades of unstructured organizational knowledge into an AI-ready knowledge base, and for designing the system that serves it. Written for the work that should happen *before* a line of pipeline is built: assessment, information architecture, governance, platform choice, and roadmap.

This is the realized form of the `groundwork` spec. The companion `worked-example.md` applies the whole method to a fictional manufacturer.

---

## The one thing to internalize first

Enterprise RAG projects rarely fail on the model. They fail on information architecture and governance: documents are stale or duplicated, access control never reaches the retrieval layer, chunks lose their table and section context, metadata is missing or inconsistent, and evaluation only ever tests the questions that were always going to work. The managed platforms (Bedrock Knowledge Bases, Azure AI Search, Vertex) all retrieve whatever they are handed. None of them decide what is authoritative, who may see it, or whether it is still true.

So the work that moves the needle is upstream of retrieval. This playbook spends most of its effort there on purpose.

---

## Phase 0 — Assessment (before designing anything)

Audit what exists before recommending a system. Five inventories:

1. **Knowledge-source inventory.** Every place knowledge lives: file shares, SharePoint, ERP/MES, email, drawings, tribal knowledge in people's heads. For each: owner, format, volume, sensitivity, update frequency, and whether it is the source of truth.
2. **Data-source inventory.** Structured systems and, increasingly, machine and sensor data: schemas, refresh cadence, access path.
3. **Process maps.** How work actually flows through each department, so retrieval can be organized around real processes rather than folder structures.
4. **Use-case capture.** The questions people actually need answered, grouped by who asks and how often. This is what readiness gets measured against.
5. **Readiness pass.** Run a goal-driven readiness scan (the `litmus` tool) per intended use case, producing a per-document and corpus-level verdict and a remediation backlog.

Output of Phase 0: a current-state map and a prioritized remediation list, not an architecture diagram. Resist the urge to design before this is done.

---

## Information architecture

The retrievable unit of an enterprise is not "a document," it is "a thing the business cares about, described by documents." Design that model explicitly.

- **Taxonomy and ontology.** Name the entities (departments, processes, equipment, products, materials, failure modes, procedures) and the relationships between them. This is what lets retrieval answer "what procedures touch this machine" rather than only "find documents similar to this query."
- **Metadata schema.** A required core set on every document: title, document type, owning department, author, created and last-reviewed dates, version, source-of-truth flag, sensitivity/classification, and the entities it describes (equipment, process, product). Optional fields per type.
- **Document standards.** A "good document" definition: a clear title, headings, tables kept as tables, no uncontrolled duplicate copies, and the metadata above. Provide a template and a controlled vocabulary so new knowledge enters clean.
- **Source-of-truth model.** For every topic, one authoritative document, with everything else marked as derived or superseded. Conflicting "truths" are the fastest way to lose user trust.

---

## Reference AI architecture

A technology-agnostic blueprint. Each stage is a place to make a deliberate decision, not a fixed product.

```mermaid
flowchart TD
  A[Sources: file shares, SharePoint, ERP/MES, drawings, sensor data] --> B[Ingestion + normalization]
  B --> C[Metadata enrichment + classification]
  C --> D[Document-type-aware chunking]
  D --> E[Index: hybrid dense + keyword]
  E --> F[Retrieval: hybrid + rerank]
  F --> G{Query type}
  G -->|relational / multi-hop| H[Knowledge graph traversal]
  G -->|structured + narrative| I[Structured query + retrieval]
  G -->|simple lookup| J[Direct retrieval]
  H --> K[Synthesis with citations]
  I --> K
  J --> K
  K --> L[Guardrails + human-in-the-loop]
  C -.governs.-> M[Governance: classification, permissions, lifecycle, provenance, audit]
  M -.enforced at.-> F
  F --> N[Evaluation + observability: retrieval quality, faithfulness, permission correctness, cost]
```

The two stages teams skip, and must not: **governance enforced at retrieval time** (not just at ingest, because permissions and document validity change after indexing), and **evaluation that measures retrieval quality separately from answer quality**, including permission correctness and cost per query, not just happy-path answers.

---

## Platform and technology selection

A decision framework, not a single answer.

- **Anchor on the cloud the organization already runs.** The authentication, networking, and compliance integration of staying on one cloud outweighs model or feature differences. AWS shop, lean Bedrock Knowledge Bases; Microsoft 365 and Azure shop, lean Azure AI Search plus Azure OpenAI.
- **Managed vs assembled.** Managed RAG (Bedrock Knowledge Bases, Azure AI Search) removes most of the pipeline and the need to operate a vector store; the trade is flexibility and some lock-in. Assemble your own (a vector store such as Qdrant or Weaviate, an orchestration layer, a reranker, RAGAS) only when a real requirement demands the control. Most teams should start managed.
- **IP-sensitive or air-gapped.** For an organization that will not let its proprietary knowledge leave its boundary, prefer private-network deployment, and keep a self-hostable option (such as Onyx) on the table. Make this an explicit requirement early, because it constrains everything downstream.
- **Cost realities.** Watch the standing costs (a managed vector store can carry a monthly baseline even at zero traffic; newer low-cost vector storage options change that math), the long-context billing cliff (retrieving too much context per query is the silent cost driver), and prompt/context caching, which materially cuts repeat-query cost.

The output is a recommendation with reasons, plus the two or three alternatives and why they were not chosen. That reasoning is the deliverable, more than the pick itself.

---

## Metadata and document standards (concrete)

Recommended core metadata, every document:

| Field | Why it matters |
|---|---|
| title, type | retrieval filtering and display |
| owning_department | scope and access |
| author, created_date, last_reviewed_date | freshness and trust |
| version, supersedes | lifecycle and conflict resolution |
| source_of_truth (bool) | authority |
| classification | governance and permission trimming |
| entities (equipment / process / product) | the ontology link that powers relational retrieval |

The "good document" standard: real title, headings, tables preserved as tables, one controlled copy, complete core metadata. Provide a one-page template and put it where new documents are created, so the corpus improves at the source rather than only in cleanup.

---

## Historical and future (sensor) data

- **Historical documents.** Triage by the readiness scan: OCR the scanned set, deduplicate, designate source-of-truth, backfill the minimum viable metadata. Do not try to perfect everything; prioritize by use-case value.
- **Future machine and sensor data.** Plan for it as a first-class, separate modality: time-series and telemetry stored and queried differently from documents, joined at answer time (structured query for the number, retrieval for the narrative). Designing the metadata and entity model now so a machine reading links to the equipment it came from is what makes the future integration cheap rather than a re-platform.

---

## Roadmap and maturity model

Crawl, walk, run. Each phase ships value and de-risks the next.

- **Crawl (foundation).** Phase 0 assessment, information architecture and standards agreed, governance model defined, readiness remediation begun, one high-value use case piloted on a managed stack with evaluation in place.
- **Walk (expand).** Broaden sources, enforce permission-aware retrieval, add the relational (graph) and structured-data paths where use cases need them, stand up observability and a feedback loop.
- **Run (operate).** Continuous ingestion and freshness, online evaluation and drift monitoring, cost governance, and a maintenance model the internal team owns.

A maturity model the client can self-assess against (sources mapped, metadata coverage, permission correctness, evaluation coverage, freshness) makes the roadmap a conversation rather than a wall of tasks.

---

## Mistakes to avoid

- Building before assessing. The assessment is the cheapest risk reduction available.
- Treating access control as a frontend concern. If permissions do not propagate into retrieval, the system will answer using documents the asker was never allowed to see.
- Letting chunking destroy structure. Tables and sections carry the meaning in technical documents.
- Happy-path evaluation. Measure retrieval quality, faithfulness, permission correctness, latency, and cost per query, not just whether the demo question works.
- Over-retrieval. Pulling too much context per query is the silent driver of both cost and degraded answers.
- Big-bang rollout. The pilot-failure rate on enterprise GenAI is high; phased delivery against one high-value use case beats a platform-wide launch.
- No source of truth. Conflicting authoritative documents erode trust faster than any model error.

---

## How to use this playbook

1. Run Phase 0 and produce the current-state map and remediation backlog.
2. Agree the information architecture, metadata schema, and document standard.
3. Define the governance model (classification, permissions, lifecycle, provenance, audit).
4. Select the platform with the decision framework and document the reasoning.
5. Pick one high-value use case, build it on the chosen stack with evaluation, and use it to validate the architecture before expanding.

See `worked-example.md` for the whole method applied end to end.
