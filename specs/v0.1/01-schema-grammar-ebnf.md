# 01. Formal Schema Grammar (EBNF)

## Purpose

The ProvenanceDB Schema Language, abbreviated as PSL, defines knowledge objects, fields, provenance blocks, trust blocks, constraints, indexes, policies, lifecycle rules and relationships.

The language is intended to be human-readable, deterministic, strongly validated, provenance-aware, and suitable for AI-assisted authoring and static analysis.

## File Extension

Recommended extension:

```text
.psl
```

## Top-Level Structure

A schema file contains one or more declarations.

```ebnf
schema          = { declaration } ;

declaration     = entity_decl
                | type_decl
                | enum_decl
                | policy_decl
                | import_decl
                | namespace_decl ;
```

## Namespaces and Imports

```ebnf
namespace_decl  = "namespace", qualified_name, ";" ;

import_decl     = "import", string_literal, ";" ;

qualified_name  = identifier, { ".", identifier } ;
```

Example:

```psl
namespace org.provenancedb.examples.resources;

import "common.psl";
```

## Entities

```ebnf
entity_decl     = "entity", identifier, "{", { entity_member }, "}" ;

entity_member   = field_decl
                | provenance_block
                | trust_block
                | lifecycle_block
                | index_decl
                | rule_decl ;
```

Example:

```psl
entity Resource {
  title: text required;
  url: url required unique;
  summary: markdown;

  provenance {
    source: ref Source required;
    evidence: list<ref Evidence>;
  }

  trust {
    score: decimal range(0, 100);
    status: enum(verified, unverified, disputed, stale);
  }
}
```

## Fields

```ebnf
field_decl      = identifier, ":", type_expr, { field_modifier }, ";" ;

type_expr       = primitive_type
                | identifier
                | "ref", identifier
                | "list", "<", type_expr, ">"
                | "map", "<", primitive_type, ",", type_expr, ">"
                | inline_enum ;

primitive_type  = "text"
                | "markdown"
                | "integer"
                | "decimal"
                | "boolean"
                | "date"
                | "datetime"
                | "url"
                | "uri"
                | "uuid"
                | "json"
                | "vector" ;

inline_enum     = "enum", "(", identifier, { ",", identifier }, ")" ;
```

## Field Modifiers

```ebnf
field_modifier  = "required"
                | "optional"
                | "unique"
                | "indexed"
                | "default", "(", literal, ")"
                | "range", "(", number, ",", number, ")"
                | "min", "(", number, ")"
                | "max", "(", number, ")"
                | "pattern", "(", string_literal, ")"
                | "derived"
                | "immutable" ;
```

## Enums

```ebnf
enum_decl       = "enum", identifier, "{", enum_value, { ",", enum_value }, "}" ;

enum_value      = identifier ;
```

Example:

```psl
enum VerificationStatus {
  verified,
  unverified,
  disputed,
  stale,
  rejected
}
```

## Custom Types

```ebnf
type_decl       = "type", identifier, "{", { field_decl }, "}" ;
```

Example:

```psl
type Citation {
  label: text required;
  url: url;
  accessed_at: datetime;
}
```

## Provenance Block

```ebnf
provenance_block = "provenance", "{", { provenance_member }, "}" ;

provenance_member = field_decl
                  | "requires", "source", ";"
                  | "requires", "evidence", ";"
                  | "track", "changes", ";"
                  | "track", "agent_actions", ";" ;
```

Required concepts:

- source
- evidence
- attribution
- created_by
- reviewed_by
- verified_at
- changed_from
- changed_to

## Trust Block

```ebnf
trust_block     = "trust", "{", { trust_member }, "}" ;

trust_member    = field_decl
                | "score", ":", trust_score_expr, ";"
                | "policy", ":", identifier, ";" ;

trust_score_expr = "weighted", "(", trust_factor, { ",", trust_factor }, ")" ;

trust_factor    = identifier, "=", number ;
```

Example:

```psl
trust {
  score: weighted(source_quality=0.35, evidence_count=0.25, recency=0.20, reviewer_confidence=0.20);
  status: enum(verified, unverified, disputed, stale);
}
```

## Lifecycle Block

```ebnf
lifecycle_block = "lifecycle", "{", { lifecycle_member }, "}" ;

lifecycle_member = "review_after", ":", duration, ";"
                 | "expires_after", ":", duration, ";"
                 | "stale_after", ":", duration, ";"
                 | "archive_after", ":", duration, ";"
                 | field_decl ;
```

Example:

```psl
lifecycle {
  review_after: 180d;
  stale_after: 365d;
}
```

## Indexes

```ebnf
index_decl      = "index", identifier, "on", "(", identifier, { ",", identifier }, ")", ";" ;
```

Example:

```psl
index by_url on (url);
index by_trust on (trust.score, trust.status);
```

## Rules and Policies

```ebnf
policy_decl     = "policy", identifier, "{", { rule_decl }, "}" ;

rule_decl       = "rule", identifier, ":", condition_expr, "=>" , action_expr, ";" ;

condition_expr  = expression ;
action_expr     = expression ;
```

Example:

```psl
policy ResourceQuality {
  rule mark_stale: now() - verified_at > 365d => status = stale;
}
```

## Literals

```ebnf
literal         = string_literal
                | number
                | boolean
                | date_literal
                | datetime_literal
                | duration ;

string_literal  = '"', { character }, '"' ;
number          = integer | decimal ;
boolean         = "true" | "false" ;
duration        = integer, ("d" | "w" | "m" | "y") ;
```

## Comments

```ebnf
comment         = line_comment | block_comment ;

line_comment    = "//", { character }, newline ;
block_comment   = "/*", { character }, "*/" ;
```

## Reserved Keywords

```text
agent
archive_after
derived
entity
enum
evidence
expires_after
find
import
index
lifecycle
namespace
policy
provenance
ref
required
review_after
rule
source
stale_after
trace
trust
type
unique
verified
where
```

## Validation Rules

A conforming PSL parser MUST:

1. reject duplicate entity names within the same namespace;
2. reject duplicate field names within the same entity;
3. require all referenced entity types to exist;
4. require numeric trust scores to define a valid range;
5. reject invalid lifecycle durations;
6. preserve provenance and trust block semantics;
7. emit structured validation errors.
