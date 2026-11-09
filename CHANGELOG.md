# Changelog

## 2026-11-10

### Added

- A before/after example in the Metadata and document standards section of `playbook.md` (spec item 5). It takes one invented machining process sheet from a file share (three copies at two revisions, no metadata, tab-aligned operations, the machine named only in a note) to a single controlled copy with every core field filled in, real headings and its operations kept as a table. A table shows what that changes for four retrieval questions, and a short list says which fields a script can derive during a backfill, which the owner should confirm, and why `last_reviewed_date` stays empty until someone has read the document.

## 2026-10-06

### Added

- Governance model section in `playbook.md`. It covers the five controls the reference architecture and "How to use this playbook" already pointed to (classification, permissions, lifecycle, provenance, audit): what to decide for each, which metadata fields carry it, where it is checked, a query-time Mermaid diagram, ownership, how to test it and the outputs of the step.
- `PROGRESS.md`, tracking the playbook against its spec, and this changelog.
- `.gitignore` for local tooling and editor files.

### Changed

- Step 3 of "How to use this playbook" now points to the Governance model section.

## 2026-06-28

### Added

- `playbook.md`, `worked-example.md` and `README.md`.
