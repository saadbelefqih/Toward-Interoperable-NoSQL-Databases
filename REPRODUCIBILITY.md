# Reproducibility Information

This repository contains the implementation and experimental configuration used for the paper:

**Toward Interoperable NoSQL Databases: A Framework for Semantic Enrichment with JSON-LD**

## Repository content

- `implementation`: main implementation of the semantic enrichment framework.
- `requirements.txt`: Python dependencies required to run the experiments.
- `experiment_config.json`: experimental configuration, dataset information, model settings, thresholds, baselines, and metrics.
- `results/`: folder intended to store generated result files.

## Datasets

The experiments use three document-oriented datasets:

1. **E-Commerce**
   - Type: synthetic dataset
   - Description: generated MongoDB-style transactional records containing users, products, and orders.
   - Role: controlled baseline with known structure and expected mappings.

2. **IoT Telemetry**
   - Type: real-world public dataset
   - Source: Environmental Sensor Telemetry Data
   - URL: https://www.kaggle.com/datasets/garystafford/environmental-sensor-data-132k

3. **Scientific Publications**
   - Type: real-world public dataset
   - Source: arXiv Dataset
   - URL: https://www.kaggle.com/datasets/Cornell-University/arxiv

Each dataset was prepared in three size variants: 1K, 10K, and 100K documents.

## Gold-standard reference mappings

The semantic mapping evaluation uses manually curated reference mappings.  
For each dataset, extracted attribute paths were compared against expected ontology correspondences or assigned to the local namespace when no reliable ontology term was available.

The reference mappings are used to compute:

- precision
- recall
- F1-score
- accuracy

The current reference set is manually curated and not independently validated through inter-annotator agreement. This limitation is acknowledged in the manuscript.

## Experimental protocol

The same pipeline is applied to all datasets:

1. Load or generate JSON documents.
2. Insert documents into the NoSQL storage layer.
3. Run schema profiling.
4. Extract attribute paths, dominant datatypes, type distributions, and field prevalence.
5. Align extracted fields with Schema.org and FOAF vocabulary terms using Sentence-BERT.
6. Generate versioned JSON-LD contexts.
7. Compare generated mappings against the reference mappings.
8. Compute semantic mapping metrics.
9. Compute ontology linking coverage.
10. Measure schema profiling time, memory usage, and throughput.
11. Run lightweight baseline methods:
    - TF-IDF cosine similarity
    - Jaccard similarity
    - Levenshtein distance

## Running the experiments

Install dependencies:

```bash
pip install -r requirements.txt