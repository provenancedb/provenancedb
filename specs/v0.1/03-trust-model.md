# 03. Trust Model

## Purpose

The ProvenanceDB trust model defines how confidence, verification, source quality, evidence strength, review status, and freshness are represented.

Trust is not treated as a vague label. It is a structured object.

## Trust Object

Example:

```json
{
  "trust": {
    "score": 86.5,
    "confidence": 0.91,
    "status": "verified",
    "evidence_count": 3,
    "source_quality": 0.88,
    "review_status": "reviewed",
    "last_verified_at": "2026-06-11T00:00:00Z"
  }
}
```

## Trust Dimensions

### Source Quality

Represents the reliability of sources.

Factors:

- primary source
- official documentation
- peer-reviewed source
- independent corroboration
- historical reliability
- publisher transparency

### Evidence Strength

Represents how strongly evidence supports a claim.

Factors:

- direct evidence
- indirect evidence
- number of supporting sources
- independence of sources
- specificity
- recency

### Reviewer Confidence

Represents human or agent confidence after review.

Values:

```text
0.00 to 1.00
```

### Freshness

Represents whether information is current.

Example:

```json
{
  "freshness": {
    "last_checked_at": "2026-06-11T00:00:00Z",
    "stale_after_days": 180,
    "status": "current"
  }
}
```

### Dispute Status

Allowed values:

```text
undisputed
disputed
partially_disputed
rejected
```

## Recommended Trust Score Formula

Default v0.1 formula:

```text
trust_score =
  (source_quality * 0.30) +
  (evidence_strength * 0.25) +
  (reviewer_confidence * 0.20) +
  (freshness * 0.15) +
  (independence * 0.10)
```

Scores are normalized to 0-100.

## Trust Status

Recommended statuses:

```text
verified
unverified
disputed
stale
rejected
archived
```

## Trust Levels

```text
90-100  high_trust
70-89   trusted
50-69   provisional
30-49   weak
0-29    unreliable
```

## Human Review

Human review SHOULD override automated scoring when documented.

A human override MUST include:

- reviewer
- timestamp
- reason
- affected fields
- previous score
- revised score

## Agent Review

Agent-generated trust scores are allowed, but MUST include:

- model or agent identifier
- prompt or task identifier
- confidence
- evidence used
- timestamp

## Staleness

Records SHOULD support review schedules.

Example:

```json
{
  "review_after_days": 180,
  "stale_after_days": 365,
  "expires_after_days": null
}
```

## Trust Transparency

A trust score MUST be explainable.

Example response:

```json
{
  "trust_score": 86.5,
  "explanation": [
    "Primary source available",
    "Three supporting evidence records",
    "Reviewed by human curator",
    "Last verified within freshness window"
  ]
}
```

## Anti-Patterns

ProvenanceDB SHOULD avoid:

- opaque trust scores
- unexplained AI confidence
- single-source verification for high-impact claims
- overwriting disputed information without preserving history
- treating popularity as truth
