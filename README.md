# Critical Reproduction of a Machine-Learning Intrusion Detection System

**Course:** Data Science in Cybersecurity  
**Instructor:** Dr. Uri Itai  
**Student:** Renata Haiek, 212744916

---

## Project Overview

This project critically reproduces and evaluates the Medium article:

> *"Enhancing Cybersecurity with Machine Learning: Analyzing Intrusion Detection Systems Using the NSL-KDD Dataset"*  
> Rudransh Srivastava, March 2025  
> https://rudransh007.medium.com/enhancing-cybersecurity-with-machine-learning-analyzing-intrusion-detection-systems-using-the-770af9dd66cf

The article claims a Random Forest achieves **99.87% accuracy** on NSL-KDD. We rebuilt the full pipeline from scratch and show that the true test-set accuracy is **76.71%** — a gap of 23.16 percentage points — consistent with training accuracy being reported as test accuracy.

**Original code:** The article provides no source code and no GitHub repository, so reproduction required re-implementing the entire pipeline from its textual description.

---

## Repository Contents

| File | Description |
|---|---|
| `IDS_DataScienceCyber.ipynb` | Full Python notebook — EDA, feature engineering, 6 models, 7 metrics, error analysis |
| `IDS_Critical_Reproduction_Report_new.pdf` | Written report — critical evaluation, reproducibility analysis, findings |
| `results_metrics.csv` | Final test-set metrics for all six models (exported by the notebook) |
| `requirements.txt` | Pinned Python dependencies |
| `KDDTrain+.txt` | NSL-KDD training set (125,973 rows) |
| `KDDTest+.txt` | NSL-KDD test set (22,544 rows) |

---

## Dataset

**NSL-KDD** — improved version of KDD Cup 1999, distributed by the Canadian Institute for Cybersecurity.

- 43 columns: 41 network features + `label` + `difficulty_level`
- Binary classification: Normal (0) vs. Attack (1)
- 4 attack families: DoS, Probe, R2L, U2R

**Source:** https://github.com/jmnwong/NSL-KDD-Dataset

---

## Feature Engineering Note (model-aware encoding)

The three nominal categorical features (`protocol_type`, `flag`, `service`) are encoded per model type rather than with a single global scheme:

- **One-hot** for the linear models (Logistic Regression, SVM) — removes the artificial ordering that integer/label encoding would impose.
- **Label encoding** for the tree ensembles (Decision Tree, Random Forest, XGBoost) — they are invariant to that ordering, and one-hot fragments the ~70-way `service` split.

Both encodings were tested on every model; the notebook documents the comparison.

---

## How to Run

### 1. Create the conda environment

```bash
conda create -n ids_project python=3.11 numpy=1.26 pandas scikit-learn matplotlib seaborn scipy -c conda-forge -y
conda activate ids_project
pip install xgboost ipykernel
python -m ipykernel install --user --name ids_project --display-name "Python (ids_project)"
```

(Or install pinned versions directly: `pip install -r requirements.txt`)

### 2. Data path

The notebook reads `KDDTrain+.txt` and `KDDTest+.txt` from the notebook's own folder. Keep the data files next to the notebook, or set `DATA_DIR` at the top of the data-loading cell to their location.

### 3. Open the notebook

```bash
jupyter notebook IDS_DataScienceCyber.ipynb
```

Select kernel: **Python (ids_project)**

### 4. Run all cells

`Kernel → Restart & Run All`

Expected runtime: ~5–10 minutes (SVM and cross-validation are the slowest steps). Running the notebook regenerates `results_metrics.csv`.

---

## Key Results

| Model | Accuracy | F1 | MCC | ROC-AUC |
|---|---|---|---|---|
| XGBoost | **81.00%** | **0.8048** | **0.665** | 0.9614 |
| Decision Tree | 77.63% | 0.7621 | 0.615 | 0.8006 |
| Naive Bayes | 77.16% | 0.7692 | 0.578 | 0.8255 |
| Random Forest | 76.71% | 0.7496 | 0.602 | **0.9634** |
| Logistic Regression | 75.46% | 0.7436 | 0.560 | 0.7852 |
| SVM (Linear) | 74.61% | 0.7323 | 0.547 | 0.7891 |

**Article claim vs. reproduction:**

| Claim | Article | Reproduced | Verdict |
|---|---|---|---|
| RF accuracy | 99.87% | 76.71% | Not reproduced |
| "SVM performed well" | stated | SVM = weakest model | Wrong |
| "Naive Bayes lowest accuracy" | stated | NB beats LR and SVM | Wrong |
| Training CV F1 (RF) | — | 0.9988 | Matches article's 99.87% |

The training CV score of **0.9988 ≈ article's 99.87%** is the primary evidence that the article reported training accuracy as test accuracy.

---

## Dependencies

See `requirements.txt`. Core stack:

```
python       3.11
numpy        1.26.4
pandas       2.2.3
scikit-learn 1.5.2
xgboost      3.2.0
matplotlib   3.9.2
seaborn      0.13.2
scipy        1.13.1
```
