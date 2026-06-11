# ProvenanceDB Schema Language (PSL)

## Goals

- Human-readable
- Strongly validated
- Provenance-aware

## Example

entity Resource {
  title: text required
  url: url required unique
  summary: markdown
  trust_score: number
  confidence: number
}

## Field Types

- text
- number
- boolean
- date
- datetime
- markdown
- url
- enum
- reference
- list

## Provenance Block

entity Resource {
  title: text

  provenance {
    source: Source
    reviewer: User
    verified: boolean
    confidence: number
  }
}

## Trust Block

trust {
  score: number
  status: enum(verified, unverified, disputed)
}
