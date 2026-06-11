# 04. Storage Architecture

## Purpose

This document defines the draft v0.1 storage architecture for ProvenanceDB.

The storage system must support durable records, append-only provenance events, schema validation, trust indexing, evidence relationships, queryable lineage, and optional vector support.

## Architectural Layers

```text
Application / Agent Layer
        |
Query Layer (PQL)
        |
Validation Layer
        |
Provenance Layer
        |
Trust Layer
        |
Storage Engine
        |
Files / Embedded Store / External Backend
```

## Storage Modes

### Local File Mode

Recommended for early implementation.

```text
.provenancedb/
  schemas/
  records/
  events/
  indexes/
  vectors/
  manifests/
```

### Embedded Engine Mode

Future implementation may use SQLite, RocksDB, LMDB, or DuckDB.

### Server Mode

Future implementation may expose HTTP, gRPC, agent protocol, and federation endpoints.

## Directory Layout

```text
.provenancedb/
├── manifest.json
├── schemas/
│   └── resources.psl
├── records/
│   └── Resource/
│       └── res_001.json
├── events/
│   └── 2026/
│       └── eventlog-0001.jsonl
├── indexes/
│   ├── by-type.idx
│   ├── by-source.idx
│   ├── by-trust.idx
│   └── by-updated.idx
├── vectors/
│   └── resource-summary.vec
└── snapshots/
    └── snapshot-2026-06-11.json
```

## Record Format

Records SHOULD be stored as canonical JSON in v0.1.

Example:

```json
{
  "id": "res_001",
  "type": "Resource",
  "version": "v1",
  "fields": {
    "title": "Example Project",
    "url": "https://example.org"
  },
  "provenance": {},
  "trust": {},
  "created_at": "2026-06-11T00:00:00Z",
  "updated_at": "2026-06-11T00:00:00Z"
}
```

## Event Log

The event log SHOULD be append-only.

Event example:

```json
{
  "event_id": "evt_001",
  "event_type": "record_created",
  "record_id": "res_001",
  "actor": "user_001",
  "timestamp": "2026-06-11T00:00:00Z",
  "payload_hash": "sha256:..."
}
```

## Indexes

Minimum v0.1 indexes:

- entity type
- record ID
- source ID
- trust score
- verification status
- updated timestamp
- stale records

## Hashing

Records and evidence SHOULD support content hashing.

Recommended hash format:

```text
sha256:<hex>
```

## Snapshots

Snapshots provide fast restore and export.

A snapshot SHOULD include schema version, record count, event log position, generated timestamp and checksum.

## Concurrency

v0.1 local file mode MAY use a write lock.

Future modes SHOULD support multi-reader, single-writer, transaction logs, optimistic concurrency and conflict detection.

## Durability

A write is durable only after:

1. validation succeeds;
2. event is appended;
3. record projection is updated;
4. relevant indexes are updated.

## Vector Support

Vectors are optional in v0.1.

Vector records SHOULD always link back to source text and provenance.

Vector search results MUST NOT be treated as verified knowledge without evidence retrieval.

## Backups

A complete backup MUST include schemas, records, event logs, indexes or index rebuild instructions, vectors or vector rebuild instructions, and the manifest.
