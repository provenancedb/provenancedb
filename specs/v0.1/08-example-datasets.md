# 08. Example Datasets

## Purpose

This document defines example datasets that demonstrate ProvenanceDB concepts.

## Dataset 1: Curated Resource Directory

Use case:

A curated directory of tools, repositories, papers, or datasets.

### Schema

```psl
entity Resource {
  title: text required;
  url: url required unique;
  category: text required indexed;
  description: markdown;
  license: text;
  maintained: boolean;
  trust_score: decimal range(0, 100);

  provenance {
    source: ref Source required;
    evidence: list<ref Evidence>;
    track changes;
    track agent_actions;
  }

  lifecycle {
    review_after: 180d;
    stale_after: 365d;
  }
}
```

### Example Record

```json
{
  "id": "res_001",
  "type": "Resource",
  "fields": {
    "title": "ExampleDB",
    "url": "https://example.org/exampledb",
    "category": "database",
    "description": "An example database project.",
    "license": "Apache-2.0",
    "maintained": true,
    "trust_score": 86.5
  }
}
```

## Dataset 2: Research Claim Registry

Use case:

Track claims, evidence, and citations for research papers.

### Schema

```psl
entity Claim {
  statement: text required;
  topic: text indexed;
  confidence: decimal range(0, 1);
  status: enum(verified, unverified, disputed, rejected);

  provenance {
    source: ref Source;
    evidence: list<ref Evidence>;
  }
}
```

## Dataset 3: AI Knowledge Memory

Use case:

Store AI-agent knowledge with explicit evidence trails.

### Schema

```psl
entity AgentMemory {
  memory: markdown required;
  agent_id: text required;
  confidence: decimal range(0, 1);
  generated_at: datetime required;

  provenance {
    source: ref Source;
    evidence: list<ref Evidence>;
    track agent_actions;
  }

  lifecycle {
    review_after: 30d;
    stale_after: 90d;
  }
}
```

## Sample PQL Queries

Find high-trust database resources:

```pql
find Resource
select title, url, trust_score
where category = "database"
and trust_score >= 80
sort trust_score desc
```

Find stale AI memories:

```pql
find AgentMemory
where lifecycle.status = "stale"
```

Explain a research claim:

```pql
explain Claim "claim_001"
```

Trace a resource:

```pql
trace Resource "res_001"
```
