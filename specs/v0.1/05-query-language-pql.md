# 05. Provenance Query Language (PQL)

## Purpose

PQL is the query language for ProvenanceDB.

PQL queries data, provenance, evidence, trust, lifecycle state, and lineage.

## Design Goals

- readable
- deterministic
- provenance-aware
- suitable for humans and agents
- convertible to structured query plans

## Basic Query

```pql
find Resource
```

## Filtering

```pql
find Resource
where trust.score >= 80
```

```pql
find Resource
where status = "verified"
and category = "database"
```

## Selecting Fields

```pql
find Resource
select title, url, trust.score
where trust.status = "verified"
```

## Sorting

```pql
find Resource
where category = "AI"
sort trust.score desc
limit 20
```

## Provenance Queries

### Trace a Record

```pql
trace Resource "res_001"
```

Returns sources, evidence, claims, review events, and lineage events.

### Explain a Record

```pql
explain Resource "res_001"
```

Returns trust explanation, supporting evidence, confidence drivers, stale warnings, and disputed claims.

### Find by Source

```pql
find Resource
from Source "src_001"
```

### Find Unverified Claims

```pql
find Claim
where verification.status = "unverified"
```

## Evidence Queries

```pql
find Evidence
supports Claim "claim_001"
```

```pql
find Resource
where evidence.count >= 2
```

## Lifecycle Queries

```pql
find Resource
where lifecycle.status = "stale"
```

```pql
find Resource
where review.due = true
```

## Agent-Oriented Queries

```pql
find Resource
where agent.flagged = true
```

```pql
explain AgentAction "act_001"
```

## Grammar Sketch

```ebnf
query          = find_query | trace_query | explain_query ;

find_query     = "find", entity_name,
                 [ from_clause ],
                 [ select_clause ],
                 [ where_clause ],
                 [ sort_clause ],
                 [ limit_clause ] ;

trace_query    = "trace", entity_name, identifier_or_string ;

explain_query  = "explain", entity_name, identifier_or_string ;

from_clause    = "from", entity_name, identifier_or_string ;

select_clause  = "select", field_path, { ",", field_path } ;

where_clause   = "where", condition ;

sort_clause    = "sort", field_path, ("asc" | "desc") ;

limit_clause   = "limit", integer ;
```

## Operators

```text
=
!=
>
>=
<
<=
contains
in
exists
missing
before
after
```

## Query Result Metadata

Every query response SHOULD include:

```json
{
  "query": "...",
  "result_count": 10,
  "generated_at": "2026-06-11T00:00:00Z",
  "warnings": [],
  "provenance_included": true
}
```

## Safety Rules

PQL SHOULD NOT silently ignore provenance constraints.

If a query returns unverified or stale information, the response SHOULD include warnings.

Example:

```json
{
  "warning": "3 results are stale and require review."
}
```

## Future Features

- graph traversal
- federated queries
- natural language to PQL
- vector-assisted retrieval
- policy-aware query permissions
