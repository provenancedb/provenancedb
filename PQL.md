# Provenance Query Language (PQL)

## Purpose

PQL is designed to query both data and provenance relationships.

## Examples

Find all resources:

find Resource

Find trusted resources:

find Resource
where trust_score > 8

Find evidence:

find Evidence
for Resource "Example Resource"

Find lineage:

trace Resource "Example Resource"

## Explain

explain Resource "Example Resource"

Returns:

- Sources
- Evidence
- Trust signals
- Review history

## Future Features

- Graph traversal
- Agent queries
- Federated search
- Natural language translation
