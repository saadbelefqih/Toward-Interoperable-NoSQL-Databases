# Toward Interoperable NoSQL Databases

This repository contains the implementation and experimental materials associated with the manuscript:

**Semantic Interoperability for Schemaless NoSQL Databases**

The project proposes a semantic enrichment framework for document-oriented NoSQL databases. The framework generates versioned JSON-LD representations from raw JSON collections by combining schema profiling, structural change detection, Sentence-BERT-based vocabulary alignment, context versioning and JSON-LD reconstruction.

---

## Overview

NoSQL databases provide flexibility for heterogeneous and evolving data, but their schemaless nature makes semantic interoperability difficult. This framework addresses this issue by:

- extracting schema fingerprints from JSON collections;
- detecting structural changes across schema versions;
- aligning extracted attributes with semantic vocabularies such as Schema.org and FOAF;
- generating versioned JSON-LD `@context` documents;
- associating each document with the appropriate context version;
- reconstructing enriched JSON-LD documents without modifying the original datastore.

The framework is designed for document-oriented NoSQL data, such as MongoDB-style JSON collections.

---

## Repository Structure

```text
.
├── implementation
├── requirements.txt
├── experiment_config.json
├── REPRODUCIBILITY.md
└── results/
