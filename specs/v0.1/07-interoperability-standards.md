# 07. Interoperability Standards

## Purpose

ProvenanceDB should interoperate with existing data, research, archival and AI ecosystems.

The goal is not to invent unnecessary formats, but to define a provenance-first model that can import from and export to widely used standards.

## Supported Export Targets

Recommended v0.1 export formats:

- JSON
- JSON Lines
- CSV
- Markdown
- RDF/Turtle
- JSON-LD
- SQLite export
- Parquet export

## Metadata Standards

ProvenanceDB SHOULD align where possible with:

- Dublin Core for general metadata
- DataCite for research datasets
- Schema.org for web resources
- W3C PROV for provenance concepts
- RO-Crate for research object packaging
- SPDX for license identifiers
- ORCID for researcher identity
- DOI for persistent scholarly identifiers

## W3C PROV Alignment

ProvenanceDB concepts can map to W3C PROV-style concepts:

```text
Knowledge Object -> Entity
Actor            -> Agent
Action/Event     -> Activity
Source           -> Entity
Evidence         -> Entity
Attribution      -> wasAttributedTo
Lineage          -> wasDerivedFrom
```

## JSON-LD Context

A future `provenancedb.context.jsonld` SHOULD define canonical terms.

Example:

```json
{
  "@context": {
    "pdb": "https://provenancedb.org/ns#",
    "source": "pdb:source",
    "evidence": "pdb:evidence",
    "trustScore": "pdb:trustScore"
  }
}
```

## License Metadata

Licenses SHOULD use SPDX identifiers.

Example:

```json
{
  "license": "Apache-2.0"
}
```

## Citation Metadata

Citation fields SHOULD support title, author, publisher, date, DOI, URL, and accessed date.

## AI/RAG Interoperability

ProvenanceDB SHOULD support export formats suitable for retrieval-augmented generation.

Each chunk MUST preserve:

- source ID
- record ID
- evidence ID
- citation
- trust status
- last verified timestamp

## Import Requirements

Importers SHOULD:

1. preserve source information;
2. create provenance events;
3. mark imported data as unverified unless explicitly verified;
4. detect duplicates;
5. validate against schema;
6. generate import reports.

## Federation

Future versions SHOULD support federation between ProvenanceDB instances.

Federated exchange SHOULD include:

- signed records
- source chains
- trust metadata
- conflict records
- verification status

## Compatibility Philosophy

ProvenanceDB should be opinionated about provenance, but open about formats.
