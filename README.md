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

---

## Repository Contents

| File | Description |
|---|---|
| `IDS_DataScienceCyber.ipynb` | Full Python notebook — EDA, feature engineering, 6 models, 7 metrics, error analysis |
| `IDS_DataScienceCyber_report.pdf` | Written report — critical evaluation, reproducibility analysis, findings |
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

## How to Run

### 1. Create the conda environment

```bash
conda create -n ids_project python=3.11 numpy=1.26 pandas scikit-learn matplotlib seaborn scipy -c conda-forge -y
conda activate ids_project
pip install xgboost ipykernel
python -m ipykernel install --user --name ids_project --display-name "Python (ids_project)"
```

### 2. Open the notebook

```bash
jupyter notebook IDS_DataScienceCyber.ipynb
```

Select kernel: **Python (ids_project)**

### 3. Run all cells

`Kernel → Restart & Run All`

Expected runtime: ~5–10 minutes (SVM and cross-validation are the slowest steps).

---

## Key Results

| Model | Accuracy | F1 | MCC | ROC-AUC |
|---|---|---|---|---|
| XGBoost | **81.00%** | **0.8048** | **0.665** | 0.9614 |
| Decision Tree | 77.63% | 0.7621 | 0.615 | 0.8006 |
| Naive Bayes | 77.16% | 0.7692 | 0.578 | 0.8255 |
| Random Forest | 76.71% | 0.7496 | 0.602 | **0.9634** |
| Logistic Regression | 75.08% | 0.7367 | 0.558 | 0.8729 |
| SVM (Linear) | 74.97% | 0.7351 | 0.557 | 0.8715 |

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

```
python      3.11
numpy       1.26.4
pandas      2.2.3
scikit-learn 1.5.2
xgboost     3.2.0
matplotlib  (any recent)
seaborn     (any recent)
scipy       (any recent)
```
