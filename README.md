# Phishing URL Detection Using Machine Learning

[![Python](https://img.shields.io/badge/Python-3.8%2B-blue)](https://www.python.org/)
[![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-orange)](https://jupyter.org/)
[![scikit-learn](https://img.shields.io/badge/scikit--learn-1.2%2B-F7931E)](https://scikit-learn.org/)
[![License](https://img.shields.io/badge/License-MIT-green)](LICENSE)

---

## Objectives

- Classify URLs as phishing or legitimate using supervised machine learning
- Compare three classifiers: Random Forest, K-Nearest Neighbors (KNN), Decision Tree
- Engineer meaningful features from raw URL strings following a defined data dictionary
- Build a reusable inference function for real-time URL classification
- Achieve best balance of precision and recall for phishing detection

---

## Data Dictionary

Features engineered per **DATAF_e0224020.pdf** (14 variables → 12 features + 1 target + 1 raw URL dropped):

| # | Variable | Type | Source / Description |
|---|----------|------|---------------------|
| 1 | URL | Text | Raw URL (engineered, then dropped) |
| 2 | Label | Categorical | Target: legitimate (0) / phishing (1) |
| 3 | URLlength | Numerical | Total URL length (length_url) |
| 4 | DomainLength | Numerical | Domain/hostname length (length_hostname) |
| 5 | IsDomainIP | Binary | Domain is IP address (ip) |
| 6 | TLD | Categorical | Top-level domain extracted from URL |
| 7 | NoOfSubDomain | Numerical | Subdomain count (nb_subdomains) |
| 8 | NoOfLettersInURL | Numerical | Letter count in URL (regex) |
| 9 | NoOfDigitsInURL | Numerical | Digit count in URL (regex) |
| 10 | NoOfEqualsInURL | Numerical | = symbol count (nb_eq) |
| 11 | NoOfQMarkInURL | Numerical | ? symbol count (nb_qm) |
| 12 | NoOfAmpersandInURL | Numerical | & symbol count (nb_and) |
| 13 | SpecialCharCount | Numerical | Non-alphanumeric, non-dot characters |
| 14 | Entropy | Numerical | Shannon entropy of URL string |

**Final model input:** 12 features + 1 target (Label). Raw URL is dropped after feature extraction.

> **Note:** The data dictionary (DATAF_e0224020.pdf) describes a 14-column subset. The actual Kaggle dataset contains 89 columns; we extract only the 14 variables defined in the dictionary.

---

## Results

| Model | Accuracy | Precision | Recall | F1-Score |
|-------|----------|-----------|--------|----------|
| **Random Forest** | **0.813** | **0.843** | **0.768** | **0.804** |
| KNN | 0.789 | 0.828 | 0.729 | 0.775 |
| Decision Tree | 0.780 | 0.783 | 0.773 | 0.778 |

**Best model:** Random Forest (F1-Score: 0.804)

**Key insights:**
- Random Forest achieves the best balance of precision and recall
- Phishing URLs tend to have higher entropy, longer lengths, and more special characters
- Top predictors: Entropy, DomainLength, URLlength, TLD

---

## Pipeline

1. **Load & Combine** — Merge Training.parquet + Testing.parquet (11,430 rows total)
2. **Feature Engineering** — Build 14 variables from raw URLs (regex, entropy, TLD extraction)
3. **Clean & Encode** — Label-encode TLD and Label; drop raw URL column
4. **Split** — 80/20 stratified train/test split
5. **Scale** — StandardScaler for KNN only (fit on train only, zero leakage)
6. **Train** — Fit Random Forest, KNN, Decision Tree
7. **Evaluate** — Accuracy, Precision, Recall, F1, Classification Reports, Confusion Matrices
7. **Compare** — Bar chart + pick best by F1-Score
8. **Interpret** — Random Forest feature importance
8. **Inference** — Reusable predict_url() function for new URLs

---

## Implementation

```bash
# 1. Clone & enter
git clone https://github.com/your-username/phishing-url-detection.git
cd phishing-url-detection

# 2. (Optional) Create environment
conda create -n phishing-detector python=3.10 -y
conda activate phishing-detector

# 3. Install dependencies
pip install pandas numpy scikit-learn matplotlib seaborn jupyter

# 4. Download data from Kaggle and place in project root:
#    Training.parquet
#    Testing.parquet

# 5. Launch Jupyter
jupyter notebook
# Open phishing_url_detection.ipynb → Run all cells
```

**Test your own URL** (last cell of notebook):
```python
url = "http://suspicious-site.com/login?redirect=bank.com"
# Re-run cell → prints majority verdict + each model's individual call
```

---

## License

This project is licensed under the MIT License — see the [LICENSE](LICENSE) file for details.

---

## Repository Structure

```
phishing-url-detection/
│
├── phishing_url_detection.ipynb   # Main notebook (run this)
├── DATAF_e0224020.pdf             # Data dictionary reference
├── .gitignore                     # Ignores data files, checkpoints, etc.
└── README.md                      # This file
```

> **Dataset not included.** The `Training.parquet` and `Testing.parquet` files are too large for GitHub. Download them from [Kaggle](https://www.kaggle.com/datasets/hemanthpingali/phishing-url) and drop them in the project root before running the notebook.