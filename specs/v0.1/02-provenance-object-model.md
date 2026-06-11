# 02. Provenance Object Model

## Purpose

The provenance object model defines how ProvenanceDB represents facts, claims, sources, evidence, attribution, review, verification and lineage.

ProvenanceDB treats provenance as data, not metadata.

## Core Objects

### Knowledge Object

A Knowledge Object is the primary stored entity.

Examples:

- Resource
- Dataset
- Claim
- Organization
- Publication
- Repository
- Person
- Event

Minimum fields:

```json
{
  "id": "res_001",
  "type": "Resource",
  "created_at": "2026-06-11T00:00:00Z",
  "updated_at": "2026-06-11T00:00:00Z"
}
```

### Claim

A Claim is a statement about a Knowledge Object.

Example:

```json
{
  "id": "claim_001",
  "subject": "res_001",
  "predicate": "has_license",
  "object": "Apache-2.0",
  "confidence": 0.92
}
```

Claims are useful when a record contains multiple statements with different evidence trails.

### Source

A Source identifies where information originated.

Examples:

- URL
- DOI
- API endpoint
- uploaded document
- repository
- dataset
- manual observation

Example:

```json
{
  "id": "src_001",
  "type": "url",
  "locator": "https://example.org/project",
  "accessed_at": "2026-06-11T00:00:00Z",
  "publisher": "Example Organization"
}
```

### Evidence

Evidence supports a Claim or Knowledge Object.

Example:

```json
{
  "id": "ev_001",
  "source_id": "src_001",
  "supports": "claim_001",
  "evidence_type": "documentation",
  "excerpt_hash": "sha256:...",
  "confidence": 0.88
}
```

### Attribution

Attribution records who or what created, modified, reviewed, or verified a record.

Attribution actor types:

- human
- agent
- system
- organization

Example:

```json
{
  "actor_id": "agent_ingest_001",
  "actor_type": "agent",
  "action": "created",
  "timestamp": "2026-06-11T00:00:00Z"
}
```

### Lineage Record

A Lineage Record captures how data changed.

Example:

```json
{
  "id": "lin_001",
  "record_id": "res_001",
  "previous_version": "v1",
  "new_version": "v2",
  "changed_by": "user_001",
  "change_reason": "Updated project license after verification",
  "timestamp": "2026-06-11T00:00:00Z"
}
```

## Relationship Model

ProvenanceDB uses a hybrid model:

```text
Knowledge Object
  -> Claim
    -> Evidence
      -> Source
  -> Trust Signal
  -> Lineage Record
  -> Review Event
```

## Minimum Provenance Requirements

Every record SHOULD include:

- unique ID
- entity type
- creation timestamp
- update timestamp
- source reference or manual-entry marker
- actor attribution
- version identifier

Every verified claim SHOULD include:

- at least one source
- at least one evidence record
- confidence score
- verification status
- verification timestamp

## Provenance Event Types

Recommended event types:

- created
- imported
- updated
- reviewed
- verified
- disputed
- rejected
- archived
- restored
- merged
- split
- agent_enriched
- agent_flagged

## Versioning

Records use monotonic version identifiers:

```text
v1
v2
v3
```

Each version MUST be recoverable or reconstructable from the event log.

## Immutability

The canonical event log SHOULD be append-only.

Materialized records MAY be updated for performance, but the event stream SHOULD preserve history.

## Manual Entry

Manual records are allowed, but they MUST include:

```json
{
  "source_type": "manual",
  "entered_by": "user_001",
  "entry_reason": "Curated addition"
}
```

## AI-Generated Data

AI-generated fields MUST be marked.

Example:

```json
{
  "summary": "A provenance-first knowledge database.",
  "summary_generated_by": "agent_summary_001",
  "summary_confidence": 0.81
}
```

## Disputed Information

A claim may have multiple competing evidence chains.

Example:

```text
Claim: Resource is actively maintained.
Evidence A: Repository commit activity suggests yes.
Evidence B: Issue tracker response time suggests no.
Status: disputed
```

ProvenanceDB SHOULD preserve disagreement rather than collapsing it prematurely.
