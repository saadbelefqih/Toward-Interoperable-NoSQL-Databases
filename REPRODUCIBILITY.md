# Reproducibility Information

This repository contains the implementation and experimental configuration for:

**Semantic Interoperability for Schemaless NoSQL Databases**

---

## Repository contents

| File / Folder | Purpose |
|---|---|
| `implementation` | Complete Python implementation of the semantic enrichment framework |
| `requirements.txt` | Python dependencies |
| `experiment_config.json` | Experimental settings (model, thresholds, dataset sizes) |
| `results/` | Output folder for generated results |

---

## System requirements

| Component | Requirement |
|---|---|
| Python | 3.9 or later |
| RAM | 8 GB minimum (16 GB recommended for 100K-document runs) |
| Disk | 2 GB free (for model cache and result files) |
| OS | Linux, macOS, or Windows |

---

## Step-by-step setup

### 1. Clone the repository

```bash
git clone https://github.com/saadbelefqih/Toward-Interoperable-NoSQL-Databases.git
cd Toward-Interoperable-NoSQL-Databases
```

### 2. (Recommended) Create a virtual environment

```bash
python -m venv venv

# Linux / macOS
source venv/bin/activate

# Windows
venv\Scripts\activate
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

The first run will automatically download the `all-MiniLM-L6-v2` model (~80 MB) from HuggingFace. An internet connection is required only for this initial download; subsequent runs use the local cache.

### 4. Run the framework smoke test

```bash
python implementation
```

Expected output:

```
============================================================
Semantic Enrichment Framework — smoke test
============================================================
  Initialising ontology index …
  Ontology index ready (40 terms).

  Inserted 4 documents into 'ecom' collection.
    Profiling: 4 docs in 0.XXXs
  Schema changes detected: True
  Context registered: 1
  ...
============================================================
Smoke test completed successfully.
============================================================
```

If this output appears with no errors, the framework is correctly installed and operational.

---

## Datasets

The paper evaluates the framework on three datasets:

### 1. E-Commerce (synthetic)

Generated programmatically by the framework. No download required. The smoke test above uses this dataset.

### 2. IoT Telemetry (public, Kaggle)

- **Source:** Environmental Sensor Telemetry Data
- **URL:** https://www.kaggle.com/datasets/garystafford/environmental-sensor-data-132k
- **Download:** requires a free Kaggle account. Use the Kaggle CLI:

```bash
pip install kaggle
kaggle datasets download garystafford/environmental-sensor-data-132k
unzip environmental-sensor-data-132k.zip -d data/iot/
```

### 3. Scientific Publications (public, Kaggle)

- **Source:** arXiv Dataset (Cornell University)
- **URL:** https://www.kaggle.com/datasets/Cornell-University/arxiv
- **Download:**

```bash
kaggle datasets download Cornell-University/arxiv
unzip arxiv.zip -d data/arxiv/
```

Each dataset is used in three size variants: 1K, 10K, and 100K documents. The framework loads and processes each variant independently.

---

## Experimental protocol

The same pipeline is applied to every dataset:

1. Load or generate JSON documents and insert them into the in-memory NoSQL storage layer.
2. Run schema profiling — extract attribute paths, dominant data types, field prevalence and nesting depth.
3. Detect structural changes relative to the previously registered schema fingerprint.
4. Align extracted field names with Schema.org, FOAF, SOSA/SSN, Dublin Core and BIBO vocabulary terms using Sentence-BERT (`all-MiniLM-L6-v2`) and FAISS cosine similarity.
5. Generate and register a versioned JSON-LD `@context` document.
6. Associate each document with its context version via the mapping management layer.
7. Reconstruct enriched JSON-LD documents on demand.
8. Evaluate semantic mapping accuracy (Precision, Recall, F1, Accuracy) against manually curated gold-standard mappings using 10-fold stratified bootstrap resampling.
9. Measure ontology linking coverage (percentage of unique attribute keys mapped to known ontology URIs).
10. Measure schema profiling time (seconds) and peak memory usage (MB) at 1K, 10K and 100K document volumes.
11. Compare against three lightweight baselines: TF-IDF cosine similarity, Jaccard similarity and Levenshtein distance.

---

## Gold-standard reference mappings

The vocabulary alignment evaluation uses manually curated reference mappings for each domain. For each dataset, attribute paths were independently assessed and either assigned to the most appropriate ontology URI (positive) or classified as unmappable (assigned to the local fallback namespace).

Metrics are computed using 10-fold stratified bootstrap resampling (80% sample per fold) to produce stable mean and standard deviation estimates.

The complete annotation file is available at gold_standard.json in the root of this repository. Every entry is derived directly from GroundTruthGenerator.schema_org_mappings, GROUND_TRUTH, and GROUND_TRUTH_EVOLUTION in JSONLD.ipynb and can be verified against those constants.

**Limitation acknowledged in the manuscript:** the reference mappings are curated by the authors and have not been subjected to independent inter-annotator agreement scoring. This is an identified direction for future work.

---

## Known issues fixed in this version

| Issue | Description | Fix applied |
|---|---|---|
| `NameError: name 'Dict' is not defined` | The original file was missing all `typing` and standard-library imports | Added complete import block at the top of the file |
| `NameError: name 'InMemoryDatabase' is not defined` | Module-level test code referenced `dbTest` before any database class was defined | Moved all class definitions before any instantiation code; removed stale test code |
| `NameError: name 'main' is not defined` | The file ended with `if __name__ == '__main__': main()` but `main()` was never defined | Implemented a complete `main()` function |
| `AttributeError: 'InMemoryDatabase' has no method 'delete_one'` | `DataStorage.delete_document` called `db.delete_one()` which was missing | Added `delete_one()` and `update_many()` to `InMemoryDatabase` |
| FAISS cosine miscalibration | Embeddings were not L2-normalised before being added to `IndexFlatIP`, causing inner products instead of cosine scores | Added per-row normalisation in both `_init_ontology_index` and `_reload_index` |
| `_reload_index` normalisation error | Applied `np.linalg.norm` to a Python list instead of a 2-D numpy array, dividing all vectors by one scalar | Stacked embeddings with `np.vstack` before computing per-row norms |

---

## Contact

For questions about the implementation or the experimental setup, please open an issue in this repository.
