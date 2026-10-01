# Toward Interoperable NoSQL Databases
 
This repository contains the implementation and experimental materials for:
 
**Semantic Interoperability for Schemaless NoSQL Databases**
 
The project proposes a semantic enrichment framework for document-oriented
NoSQL databases. The framework generates versioned JSON-LD representations
from raw JSON collections by combining schema profiling, structural change
detection, Sentence-BERT-based vocabulary alignment, context versioning and
JSON-LD reconstruction.
 
---
 
## Overview
 
NoSQL databases provide flexibility for heterogeneous and evolving data, but
their schemaless nature makes semantic interoperability difficult. This
framework addresses this by:
 
- extracting schema fingerprints from JSON collections
- detecting structural changes across schema versions
- aligning extracted attributes with semantic vocabularies (Schema.org, FOAF) using Sentence-BERT
- generating versioned JSON-LD `@context` documents
- associating each document with the appropriate context version
- reconstructing enriched JSON-LD documents without modifying the original datastore
---
 
## Repository structure
 
```
.
├── implementation          # Complete Python implementation (run this)
├── requirements.txt        # Python dependencies
├── experiment_config.json  # Model settings, thresholds, dataset sizes
├── gold_standard.json      # Manually curated vocabulary annotation file
├── REPRODUCIBILITY.md      # Step-by-step setup and run instructions
└── results/                # Output folder for generated files
```
 
---
 
## Quick start
 
```bash
git clone https://github.com/saadbelefqih/Toward-Interoperable-NoSQL-Databases.git
cd Toward-Interoperable-NoSQL-Databases
pip install -r requirements.txt
python implementation
```
 
See **REPRODUCIBILITY.md** for full setup instructions, expected output,
dataset download commands and the experimental protocol.
 
---
 
## Gold standard
 
`gold_standard.json` contains 73 manually curated field-to-ontology-URI
mappings across three collections (users, products, orders). It is used to
compute Precision, Recall, F1-score and Accuracy for the vocabulary alignment
evaluation. Every entry is directly traceable to the `GroundTruthGenerator`
and `GROUND_TRUTH` constants in the original implementation notebook.
 
---
 
## Framework components
 
| Component | Class | Responsibility |
|---|---|---|
| Raw Data Storage | `DataStorage` | Persist raw JSON documents |
| Schema Profiling | `SchemaProfiler` | Extract structural fingerprints and detect changes |
| Vocabulary Mapping | `VocabularyMapper` | Align fields to ontology terms via SBERT + FAISS |
| Context Registry | `ContextRegistry` | Store and version JSON-LD @context documents |
| Mapping Management | `MappingManager` | Associate documents with their context version |
| JSON-LD Reconstruction | `JsonLdService` | Produce enriched JSON-LD outputs on demand |
 
---
## About
 
Toward Interoperable NoSQL Databases: A Framework for Semantic Enrichment
with JSON-LD
