# Project Overview and PDR

Last updated: 2026-11-03. Status: NER, IMDb and SST-2 notebooks on `main` (SST-2 merged at commit 06c4057).

## Purpose

IT4772E is a course repository for capstone projects. Each project lives in its own top-level folder and is listed in the root `README.md`.

## Projects

| Folder | Task | Artifact | Status |
|---|---|---|---|
| `NER/` | Named entity recognition (PER, LOC, ORG, MISC) with a linear-chain CRF on CoNLL-2003 (English) | `NER/CoNLL2003.ipynb` | Done |
| `Sentiment/` | IMDb review sentiment: BERT vs TF-IDF + LR | `Sentiment/IMDb.ipynb` | Done |
| `Sentiment/` | SST-2 (GLUE) sentence sentiment: BERT vs TF-IDF + LR | `Sentiment/SST2.ipynb` | Done |

## Project 1: NER with CRF

### Goal

Teach and demonstrate a CRF-based NER pipeline: data loading, IOB format handling, feature engineering, training, evaluation, hyperparameter search, and model inspection. Written in Vietnamese with step-by-step explanations.

### Functional Requirements

| ID | Requirement | Status |
|---|---|---|
| FR-1 | Install dependencies from within the notebook | Done (cell 3) |
| FR-2 | Download CoNLL-2003 train/dev/test files if absent | Done (cells 7-8) |
| FR-3 | Convert IOB1 labels to IOB2 | Done (cell 11) |
| FR-4 | Report dataset statistics (documents, sentences, tokens, entities) | Done (cell 13) |
| FR-5 | Train a baseline CRF and report token-level and entity-level F1 | Done (cells 31-37) |
| FR-6 | Tune `c1`/`c2` on the official dev split | Done (cell 45) |
| FR-7 | Retrain with the best parameters and evaluate once on test | Done (cell 50) |
| FR-8 | Show top transition and state weights | Done (cells 52-55) |

### Non-Functional Requirements

| ID | Requirement |
|---|---|
| NFR-1 | Runs top to bottom in a fresh Python 3 environment with network access |
| NFR-2 | Reproducible: `random_state=42` for the search |
| NFR-3 | Tuning size set by `N_ITER = 50`; the notebook comment suggests 10 on slow machines |
| NFR-4 | No dataset files committed; `data/` is gitignored |
| NFR-5 | Attribution to the upstream MIT-licensed notebook is kept in the notebook header |

### Verified Results

Taken from the saved outputs in the notebook (`random_state=42`, scikit-learn 1.9.1). Re-running on another library version may shift numbers slightly.

| Model | Dev entity F1 | Test entity F1 | Test token F1 (O excluded) |
|---|---|---|---|
| Baseline `c1=0.1`, `c2=0.1` | not reported | 0.7902 | 0.8014 |
| Tuned `c1≈0.00277`, `c2≈0.0845` | 0.877 | 0.7946 | 0.8071 |

Dataset sizes (from notebook output):

| Split | Documents | Sentences | Tokens | Entities |
|---|---|---|---|---|
| train | 946 | 14,041 | 203,621 | 23,499 |
| dev (`eng.testa`) | 216 | 3,250 | 51,362 | 5,942 |
| test (`eng.testb`) | 231 | 3,453 | 46,435 | 5,648 |

### Acceptance Criteria

- Notebook runs top to bottom without errors on the listed packages.
- Re-run numbers match the Verified Results table on the same library versions.
- Hyperparameter search (cells 44-48) scores on dev only; the test split is evaluated after the final fit.

### Constraints and Dependencies

- Python 3 (notebook metadata: 3.11.13; author also ran 3.12).
- `sklearn-crfsuite >= 0.5.0`, `scikit-learn >= 1.3`, `scipy`, `seqeval`, `matplotlib`.
- Network access to `raw.githubusercontent.com` on first run.
- Runtime: tuning cell about 1.5 min wall time on the author's machine; full notebook about 3-4 min.
- The Hugging Face `conll2003` loader is not used; its upstream data host is no longer available. The glample/tagger mirror has altered labels and is also not used.

### Known Limitations

- The test split comes from a later time period (December 1996) than train and dev (August 1996), so test F1 is lower than dev F1. This is a property of the dataset, not a bug.
- The learned model relies on dates from August 1996 (e.g. weight on `+1:word.lower():1996-08-22`), which do not transfer to the December 1996 test set. The notebook (section 8) cites this as time-based overfitting. The notebook suggests a generic date-shape feature as a fix; it is not implemented.
- Features are hand-crafted; no neural model or embeddings are included.

## Project: SST-2 Sentiment with BERT

Fine-tunes `bert-base-uncased` on GLUE SST-2 and compares it with TF-IDF + Logistic Regression. Train is GLUE `train` (67,349 phrases); GLUE `validation` (872 sentences) selects hyperparameters and the epoch; GLUE `test` (1,821 sentences) is the final evaluation.

GLUE hides test labels (`-1`). The notebook restores them from `SetFit/sst2` by normalized sentence text and stops if any sentence fails to match or if validation labels disagree between the two sources.

Verified results from the saved notebook outputs (seed 42, one run, Google Colab Tesla T4, Python 3.13.16, torch 2.11.0, transformers 5.22.0, scikit-learn 1.6.1):

| Split | Model | Accuracy | Precision | Recall | F1 |
|---|---|---|---|---|---|
| Validation | TF-IDF + LR | 0.8326 | 0.8130 | 0.8716 | 0.8413 |
| Validation | BERT | 0.9266 | 0.9279 | 0.9279 | 0.9279 |
| Test | TF-IDF + LR | 0.8232 | 0.7968 | 0.8669 | 0.8303 |
| Test | BERT | 0.9396 | 0.9210 | 0.9615 | 0.9408 |

Test accuracy difference: +11.6 points (bootstrap 95% CI 9.8 to 13.5); McNemar exact p ≈ 1.4e-35.

## Open Questions

- No `LICENSE` file. The notebook is derived from an MIT-licensed upstream, so a license decision is needed before the repo is reused outside the course.
- CoNLL-2003 text is Reuters material; the repo only ships the notebook and downloads the data at runtime. Confirm this is acceptable for a public GitHub repo (https://github.com/ducnt3/IT4772E).
