# Project Changelog

Format: newest first. Dates are local (Asia/Ho_Chi_Minh).

## 2026-11-03

### Added
- `Sentiment/SST2.ipynb`: BERT fine-tuning on GLUE SST-2 compared with TF-IDF + Logistic Regression. Test labels are restored from `SetFit/sst2`. Commits `bc3d666`, `06c4057`; merged to `main`.
- `Sentiment/SST2.ipynb` follow-up: pinned library versions, leakage check (whole-phrase and shared 5-gram), C check beyond the grid edge, mutually exclusive negation/contrast groups with paired bootstrap CIs. Re-run on Colab Tesla T4 reproduced the same BERT numbers.

### Changed
- `.gitignore`: adds `credentials/` and `docs/session-handoffs/` (session state, not project docs).
- `Sentiment/SST2.ipynb`: outputs refreshed from a second Colab re-run; results unchanged.

## 2026-10-15

### Added
- `NER/CoNLL2003.ipynb`: CRF named entity recognition on CoNLL-2003 (English). Adapted from the sklearn-crfsuite CoNLL-2002 notebook (MIT). Includes IOB1 to IOB2 conversion, EDA, baseline CRF, entity-level evaluation with seqeval, and `RandomizedSearchCV` tuning on the official dev split. Commit `cfb3de7`.
- `README.md`: project index table and note that datasets are downloaded at runtime. Commit `cfb3de7`.
- `.gitignore`: excludes `data/` (Reuters licence), notebook checkpoints, virtualenvs, `__pycache__/`, `.DS_Store`. Commit `cfb3de7`.
- `docs/codebase-summary.md`, `docs/project-overview-pdr.md`, `docs/project-changelog.md`: initial project documentation.

### Known Issues
- No `LICENSE` file.
- Test F1 (0.79) is lower than dev F1 (0.88) because the test split is from a later period. See [project overview](./project-overview-pdr.md#known-limitations).
