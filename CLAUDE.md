# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

Academic project (BRACIDA) that builds an early-warning system to predict student dropout (evasão escolar). All work lives in a single Jupyter notebook, `Projeto-Dropout.ipynb`, trained on `student dropout.csv` (one row per student, target column `Dropped_Out`). The project is documented and labeled in **Portuguese** — keep new code, comments, and class labels in Portuguese for consistency.

## Working with the notebook

- Run/edit cells in `Projeto-Dropout.ipynb` (Python 3, kernel `python3`). There is no build, test, or lint setup — validation is done by re-running the notebook top to bottom and inspecting the classification report and confusion matrix.
- Dependencies: `pandas`, `numpy`, `seaborn`, `matplotlib`, `scikit-learn`, `joblib`. Install ad hoc (`pip install ...`); there is no requirements file.
- The trained model is serialized to `modelo_evasao.joblib`. `*.joblib` is gitignored — re-running the save cell regenerates it; downstream cells load it back for the `prever_evasao` deployment function.

## ML pipeline architecture

The model is a single scikit-learn `Pipeline` wrapped in `GridSearchCV`, structured so the same fitted object handles preprocessing and inference end to end:

1. **Leakage guard** — `X` drops `Final_Grade` and `Grade_2` in addition to the target. These are zero for students who left mid-year, so keeping them would let the model "cheat." Do not reintroduce them as features.
2. **Column typing** — numeric columns are selected via `np.number`; everything else is treated as categorical (`cat_cols = [c for c in X.columns if c not in num_cols]`). This pattern is intentional to avoid a pandas 3.0 deprecation warning — prefer it over `select_dtypes(include=['object','category'])`.
3. **Preprocessing** — `ColumnTransformer` with `OneHotEncoder(handle_unknown='ignore')` for categoricals and `StandardScaler` for numerics.
4. **Model** — `LogisticRegression(solver='liblinear', class_weight='balanced', max_iter=1000, random_state=42)`. `class_weight='balanced'` is deliberate: dropouts are the minority class.
5. **Tuning** — `GridSearchCV(cv=5, scoring='f1', n_jobs=-1)`. F1 (not accuracy) is the optimization target because the classes are imbalanced and recall on the dropout class matters most.
6. **Split** — `train_test_split(test_size=0.3, random_state=42)`. Keep `random_state=42` everywhere for reproducible results.

## Label convention

Class labels were standardized to **"Não Evasão"** (`False`/0) and **"Evasão"** (`True`/1) across all plots, the `classification_report` (`target_names=['Não Evasão', 'Evasão']`), the confusion matrix (`display_labels`), and the `prever_evasao` output. Maintain this exact wording — avoid older variants like "Estável" / "Risco de Evasão". `ALTERACOES_REALIZADAS.md` records the before/after of these renames cell by cell.

## Reference docs

- `ALTERACOES_REALIZADAS.md` — changelog of notebook edits (label renames, deprecation fixes) with the rationale per cell.
- `Instruções Projeto.pdf`, `Artigo - ...docx`, `BRACIDA - .pdf` — project requirements and the accompanying paper.
