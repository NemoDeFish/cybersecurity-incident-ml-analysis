# Cybersecurity Incident Classification - Machine Learning Analysis

This project applies machine learning to real-world SOC telemetry to reduce false positives and prioritize high-risk security incidents. Using supervised classification and MITRE ATT&CK–based pattern mining on **100,000 incidents sampled from a 13M+ record dataset**, the system identifies which alerts are most likely to represent true threats and highlights the most dangerous adversarial behaviors.

📄 **Full technical report:**
[Cybersecurity_Incident_Classification.pdf](report/Cybersecurity_Incident_Classification_Report.pdf)

---

## Overview

Security Operations Centers (SOCs) process enormous volumes of alerts every day, many of which do not represent real threats. Accurately identifying **true positive (TP)** incidents is critical for reducing analyst workload and ensuring timely response to high-risk attacks.

This project combines:

- **Interpretable supervised learning** for incident classification
- **MITRE ATT&CK–aligned unsupervised analysis** for adversarial behavior discovery
- **Temporal feature evaluation** across multiple model families

---

## Key Findings (Summary)

- **Decision Trees achieved 77% accuracy**, substantially outperforming Naïve Bayes on incident classification.
- **Detection and organizational metadata** dominate predictive power, accounting for over 90% of feature importance.
- **Credential abuse (Valid Accounts)** and **obfuscation-based techniques** form the highest-risk MITRE ATT&CK clusters.
- **Time-based features improve Logistic Regression and ANN performance**, but degrade linear SVM under class imbalance.
- Risk is driven more by _which techniques are used_ than by the number of techniques in an attack pattern.

---

## Why This Matters

SOC teams operate under extreme alert volume and limited analyst time.
This project demonstrates how **interpretable ML models** and **MITRE ATT&CK–aligned analysis** can:

- Reduce analyst fatigue by prioritizing true threats
- Explain _why_ an alert is risky, not just assign a score
- Bridge ML outputs with frameworks already used by security teams

---

## Project Objectives

1. **Feature Impact Analysis**
   Identify which categories of alert metadata (Organization, Detection, Entity, Account, Network, Location) most strongly predict true security incidents.

2. **Adversarial Behavior Analysis**
   Discover high-risk MITRE ATT&CK technique combinations observed in confirmed incidents using clustering and risk-weighted pattern analysis.

3. **Temporal Signal Evaluation**
   Measure how time-based features affect classification performance across linear, margin-based, and neural models.

---

## Dataset

This project uses the **Microsoft Security Incident Prediction Dataset** (Freitas et al., 2024), one of the largest publicly available collections of real-world SOC telemetry.

- ~13 million telemetry records
- ~1 million labeled incidents (TP / BP / FP)
- 6,100 organizations
- 441 MITRE ATT&CK techniques
- Collected from production Microsoft Defender XDR environments

🔗 **Dataset:**
[Microsoft Security Incident Prediction – Kaggle](https://www.kaggle.com/datasets/Microsoft/microsoft-security-incident-prediction/data)

---

## Repository Structure

```
├── README.md
│
├── dataset/
│   ├── sample_100000.csv        # Stratified sample
│   ├── train.csv                # Training split
│   └── test.csv                 # Test split
│
├── preprocessing/
│   ├── dataset.py               # Sampling & train/test split
│   ├── timestamp.py             # Time feature processing
│   ├── DataProcessStrategy.py   # Feature strategies (with/without time)
│   ├── mitre_feature_engineering.py
│   └── Data Reproduction Guide.md
│
├── models/
│   ├── classifier_trainer.py    # Base trainer utilities
│   ├── LogisticRegressionTrainer.py
│   ├── SVMTrainer.py
│   ├── ANNTrainer.py
│   ├── KMeans.py                # MITRE technique clustering
│   └── ResultQ3.py              # Time-feature experiments
│
├── output/
│   └── TechniquePatternPlotting.py
│
├── report/
│   └── Incident_Prediction_and_MITRE_Analysis.pdf
│
└── requirements.txt
```

---

## Models Used

### Supervised Learning

- **Decision Tree** — interpretability and feature attribution
- **Naïve Bayes** — probabilistic baseline and mutual information analysis
- **Logistic Regression** — linear baseline under regularization
- **Linear Support Vector Machine** — margin-based classifier in sparse spaces
- **Multi-Layer Perceptron (ANN)** — non-linear interaction modeling

### Unsupervised Learning

- **K-Means Clustering** — MITRE ATT&CK technique co-occurrence analysis
    - Risk-weighted using a heuristic informed by MITRE documentation
    - Cluster quality evaluated via silhouette score

---

## Quick Start

### 1) Installation

```bash
pip install -r requirements.txt
```

---

### 2) Data Preparation

Stratified sampling and train/test split:

```bash
python preprocessing/dataset.py \
  --input-csv GUIDE_Test.csv \
  --out-sample dataset/sample_100000.csv \
  --out-train dataset/train.csv \
  --out-test dataset/test.csv \
  --sample-size 100000 \
  --train-size 80000 \
  --test-size 20000 \
  --seed 2025
```

Detailed reproduction steps are documented in:
`preprocessing/Data Reproduction Guide.md`

---

### 3) MITRE Feature Engineering

Build MITRE ATT&CK risk features
(_expects `all_techniques.csv` in the same folder_):

```bash
python preprocessing/mitre_feature_engineering.py
```

---

### 4) Feature Group Importance

Decision Tree:

```bash
python models/DecisionTree.py
```

Naïve Bayes:

```bash
python models/NaiveBayes.py
```

---

### 5) MITRE Technique Pattern Clustering

```bash
python models/KMeans.py
```

Visualization:

```bash
python -m output.TechniquePatternPlotting
```

---

### 6) Time-Based Feature Experiments

Run ANN, Logistic Regression, and SVM with and without time features:

```bash
python models/ResultQ3.py
```

---

## Authors

- Si Yong Lim
- Ella McKercher
- Peiyu Wang
- Yaxi Bai
