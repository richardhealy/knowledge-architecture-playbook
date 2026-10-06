# Progress

The spec for this playbook is the `groundwork` page in the AI Portfolio Projects database in Notion. It lists nine contents items and names governance as one of the five parts of the architect's job (assessment, information architecture, governance, platform reasoning, roadmap).

## Against the spec

| Spec item | State |
|---|---|
| 1. Assessment framework | Done (Phase 0 in `playbook.md`) |
| 2. Information architecture | Done |
| 3. Reference AI architecture, diagrammed | Done (Mermaid flowchart) |
| Governance model (classification, permissions, lifecycle, provenance, audit) | Done 2026-10-06 (Governance model section, with a query-time diagram) |
| 4. Platform selection | Done |
| 5. Metadata and document standards | Partly. The core fields and the "good document" standard are there. The before/after example the spec asks for is missing. |
| 6. Historical and future sensor data | Done |
| 7. Roadmap and maturity model | Partly. The crawl, walk, run roadmap is there. The maturity model is one sentence listing five dimensions, with no levels to self-assess against. |
| 8. Mistakes to avoid | Partly. The spec asks for the pilot-failure statistic; the playbook only says the rate is high, with no figure or source. |
| 9. Worked example | Done (`worked-example.md`) |

## Known issues

- `README.md`, `playbook.md` and `worked-example.md` refer to `litmus` (readiness scanner) and `steward` (governance layer) as if they exist. Neither has a repository yet; both are "Not started" in Notion. The nearest working readiness tool is the `readiness` CLI in `enterprise-ai-architect-application`.
- The original text uses American spelling and some em dashes. Text added since 2026-10-06 uses British spelling, so the two are mixed until a spelling pass is done.
- The README has no status, known limits or links to this file and the changelog, and there is no dossier yet.

## Next

1. A before/after metadata example (spec item 5).
2. A maturity model with levels per dimension (spec item 7).
3. A sourced pilot-failure figure in Mistakes to avoid (spec item 8).
4. README refresh, including an honest note on `litmus` and `steward`.
5. Project dossier under `docs/dossier/`.
