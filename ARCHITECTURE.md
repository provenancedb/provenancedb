# ProvenanceDB Architecture

## Overview

ProvenanceDB is a provenance-first knowledge database.

Traditional databases focus on data storage.

ProvenanceDB focuses on:

- Data
- Sources
- Evidence
- Trust
- Lineage
- Explainability

## Conceptual Model

Entity
  -> Facts
  -> Sources
  -> Evidence
  -> Trust Signals
  -> Change History

Every record maintains relationships to its origins and supporting evidence.

## Core Objects

### Resource

Represents a knowledge object.

Examples:

- Article
- Dataset
- Repository
- Tool
- Organization

### Source

Represents where information originated.

Examples:

- URL
- Publication
- Dataset
- API

### Evidence

Supports a claim.

Examples:

- Citation
- Observation
- Document

### Trust Signal

Represents confidence and verification metadata.

Examples:

- Verification status
- Review history
- Confidence score

## Example Schema

entity Resource {
  title: text required
  url: url required
  summary: markdown
  trust_score: number
  sources: list<Source>
}

## Storage Layers

1. Schema Layer
2. Record Layer
3. Provenance Layer
4. Trust Layer
5. Query Layer

## Future Components

- Provenance Query Language (PQL)
- AI Enrichment Engine
- Agent SDK
- Distributed Trust Network
