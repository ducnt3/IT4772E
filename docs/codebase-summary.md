# Codebase Summary

Last updated: 2026-11-03. Course repo (HUST, IT4772E). Current content: `NER/` and `Sentiment/` (`IMDb.ipynb`, `SST2.ipynb`).

## Repository Layout

```
IT4772E/
├── README.md              # Project index (table of capstone folders)
├── .gitignore             # Ignores data/, notebook checkpoints, venvs, credentials/, docs/session-handoffs/
├── docs/                  # Project documentation (this folder)
│   └── session-handoffs/  # Controller-managed session state; gitignored
├── NER/                   # Capstone 1: named entity recognition with CRF
    ├── CoNLL2003.ipynb    # Single self-contained notebook (57 cells)
    └── data/conll2003/    # Created at runtime by the notebook; gitignored
        ├── eng.train      # CoNLL-2003 train split
        ├── eng.testa      # Official dev split (named testa in the original release)
        └── eng.testb      # Official test split (named testb in the original release)
└── Sentiment/             # Sentiment classification: BERT vs TF-IDF + Logistic Regression
    ├── IMDb.ipynb         # IMDb movie reviews
    ├── SST2.ipynb         # GLUE SST-2 sentences
    └── data/              # Created at runtime; gitignored
```

## Files

| Path | Type | Purpose |
|---|---|---|
| `README.md` | Markdown | Lists projects; links to each folder and notebook; notes that data is downloaded at runtime |
| `.gitignore` | Config | Excludes `data/` (Reuters licence), `.ipynb_checkpoints/`, `__pycache__/`, `.venv/`, `venv/`, `.DS_Store`, `credentials/`, `docs/session-handoffs/` |
| `NER/CoNLL2003.ipynb` | Jupyter notebook | Full NER pipeline |
| `Sentiment/IMDb.ipynb` | Jupyter notebook | IMDb sentiment comparison |
| `Sentiment/SST2.ipynb` | Jupyter notebook | SST-2 sentiment comparison |

No Python packages, modules, tests, or build scripts exist outside the notebooks.

## Notebook Structure (`NER/CoNLL2003.ipynb`)

| Section | Cells | What it does |
|---|---|---|
| 0. Overview and credits | md | Task description, attribution to sklearn-crfsuite (MIT), data licence note |
| 1. Setup | 3–6 | `pip install` of `sklearn-crfsuite>=0.5.0`, `scikit-learn>=1.3`, `scipy`, `seqeval`, `matplotlib`; imports |
| 2. Data loading | 7–10 | Downloads `eng.train`, `eng.testa`, `eng.testb` from the synalp/NER mirror into `data/conll2003/` if missing |
| IOB1 to IOB2 | 11–12 | `iob1_to_iob2()` converts `I-X` to `B-X` at entity starts; `read_conll()` parses the 4-column files |
| 3. EDA | 13–24 | Document/sentence/token/entity counts, entity-type counts, label distribution, length stats, unseen-entity rate, POS by type |
| 4. Features | 26–31 | `word2features(sent, i)` (window of ±1 token, prefixes/suffixes, case flags, POS); builds `X_*`/`y_*` for all splits |
| 5. Baseline CRF | 32–33 | `CRF(algorithm='lbfgs', c1=0.1, c2=0.1, max_iterations=100, all_possible_transitions=True)` trained on train |
| 6. Evaluation | 34–43 | Token-level weighted F1 (O removed) via `sklearn_crfsuite.metrics`; entity-level F1 via `seqeval`; per-sentence diff |
| 7. Hyperparameter search | 44–48 | `RandomizedSearchCV`, `N_ITER=50`, `PredefinedSplit` (train = -1, dev = 0), `refit=False`, `random_state=42`; log-scale scatter plot |
| 7b. Final model | 50–51 | Retrains on train with `best_params_`; reports model size and test scores |
| 8. Model inspection | 52–56 | Top transition and state features from `crf.transition_features_` and `crf.state_features_` |

## Data Flow

```
synalp/NER (GitHub raw)
  -> data/conll2003/eng.{train,testa,testb}
  -> read_conll() + iob1_to_iob2()
  -> sent2features() -> X_train, X_dev, X_test
  -> CRF.fit(train) -> predict(test) -> token F1 / entity F1
  -> RandomizedSearchCV on dev -> best (c1, c2) -> refit on train -> test
```

## Dependencies

| Package | Source in notebook |
|---|---|
| sklearn-crfsuite >= 0.5.0 (pulls python-crfsuite) | Cell 3 |
| scikit-learn >= 1.3 (verified with 1.9.1) | Cell 3 |
| scipy, matplotlib | Cell 3 |
| seqeval | Cell 3 |

Python: notebook metadata records 3.11.13; the author reports identical results on 3.11 and 3.12.

## Generated and Ignored Paths

- `NER/data/conll2003/` is created on first run and is gitignored. Do not commit it.
- Notebook checkpoints and virtualenvs are ignored by `.gitignore`.
- `credentials/` and `docs/session-handoffs/` are gitignored. Never commit them.
