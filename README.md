# Knowledge Architecture Playbook

A repeatable method for making decades of unstructured organizational knowledge AI-ready, and for designing the knowledge-base system that serves it. Built for the architect's job: assessment, information architecture, governance, platform selection, and roadmap, before any build.

- `playbook.md` — the method and a technology-agnostic reference architecture.
- `worked-example.md` — the whole method applied end to end to a fictional manufacturer.

The premise: enterprise RAG fails on information architecture and governance, not on the model. So the work that matters is upstream of retrieval, and that is where this playbook spends its effort.

This pairs with three implementation projects: a goal-driven document-readiness scanner (`litmus`), a governance layer that enforces classification, permissions, lifecycle, and provenance at retrieval time (`steward`), and a measured advanced-retrieval foundation (`quarry`).
