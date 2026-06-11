# ProvenanceDB v0.1 Specification Package

**Trust Every Fact. Trace Every Source.**

This package defines the v0.1 technical specification for ProvenanceDB, a provenance-first knowledge database designed for trusted knowledge systems, curated datasets, research repositories, and AI/agent-native workflows.

## Package Contents

- `01-schema-grammar-ebnf.md` — formal schema grammar using EBNF
- `02-provenance-object-model.md` — provenance entities, claims, evidence, and lineage
- `03-trust-model.md` — trust scoring, confidence, verification, and review model
- `04-storage-architecture.md` — storage layers, files, indexes, and durability model
- `05-query-language-pql.md` — Provenance Query Language specification
- `06-agent-protocol.md` — AI/agent interaction model
- `07-interoperability-standards.md` — export/import and standards alignment
- `08-example-datasets.md` — example datasets and schema walkthrough
- `examples/` — sample schema, records, and queries

## Status

Version: `0.1-draft`

This specification is intentionally conservative. It defines the conceptual and technical foundation before implementation details are finalized.

## Design Goals

ProvenanceDB is designed to make the following first-class:

- facts
- claims
- sources
- evidence
- attribution
- confidence
- verification
- trust
- lineage
- review history
- AI/agent actions

## Non-Goals

ProvenanceDB v0.1 is not intended to replace PostgreSQL, MongoDB, SQLite, or dedicated vector databases.

Instead, it defines a knowledge database model for cases where the origin, trustworthiness, and explainability of information matter as much as storage and retrieval.
