# 06. Agent Protocol Specification

## Purpose

The ProvenanceDB Agent Protocol defines how AI agents interact with ProvenanceDB.

Agents may ingest, enrich, classify, verify, summarize, flag or query records.

Every agent action must be attributable, auditable and reversible where possible.

## Agent Identity

Each agent MUST have an identity.

Example:

```json
{
  "agent_id": "agent_ingest_001",
  "name": "Resource Ingestion Agent",
  "version": "0.1.0",
  "provider": "local",
  "capabilities": ["ingest", "summarize", "classify"]
}
```

## Agent Action Record

Every agent action MUST create an action record.

```json
{
  "action_id": "act_001",
  "agent_id": "agent_ingest_001",
  "action_type": "summarize",
  "target_record": "res_001",
  "inputs": ["title", "url", "source_text"],
  "outputs": ["summary"],
  "confidence": 0.82,
  "timestamp": "2026-06-11T00:00:00Z"
}
```

## Required Agent Metadata

Agent actions MUST record:

- agent ID
- agent version
- action type
- target record
- input references
- output fields
- confidence
- timestamp
- evidence used
- whether the action modified data

## Agent Permissions

Recommended permission scopes:

```text
read
suggest
write_draft
write_verified
flag
review
admin
```

Default agents SHOULD operate in `suggest` mode.

## Agent Action Types

Recommended types:

- ingest
- extract
- classify
- summarize
- embed
- deduplicate
- verify
- flag_stale
- flag_dispute
- recommend_merge
- recommend_schema_change

## Suggested vs Applied Changes

Agents SHOULD produce suggestions by default.

Example:

```json
{
  "suggestion_id": "sug_001",
  "type": "field_update",
  "field": "summary",
  "proposed_value": "A provenance-first knowledge database.",
  "confidence": 0.84,
  "requires_human_review": true
}
```

## Human Approval

Applied agent suggestions SHOULD include approval metadata.

```json
{
  "approved_by": "user_001",
  "approved_at": "2026-06-11T00:00:00Z",
  "approval_note": "Summary is accurate."
}
```

## Agent Query Protocol

Agents may query using PQL.

Request:

```json
{
  "agent_id": "agent_verify_001",
  "query": "find Resource where trust.status = "unverified" limit 10"
}
```

Response:

```json
{
  "results": [],
  "warnings": [],
  "provenance_required": true
}
```

## Agent Safety Requirements

Agents MUST NOT:

- overwrite verified human-reviewed data without policy permission;
- remove evidence without recording lineage;
- mark claims verified without evidence;
- hide uncertainty;
- collapse disputes without preserving competing claims.

## Agent Output Labelling

AI-generated fields MUST be labelled.

Example:

```json
{
  "field": "summary",
  "value": "Example summary.",
  "generated_by": "agent_summary_001",
  "confidence": 0.80,
  "requires_review": true
}
```

## Agent Interoperability

Future versions SHOULD support external agent registration, signed agent actions, agent capability discovery, cross-instance verification and federated trust updates.
