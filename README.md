# Cybersecurity Incident Classification - Machine Learning Analysis

## Overview

Security Operations Centers (SOCs) receive large volumes of alerts daily, many of which do not represent real threats. Accurately identifying **true positive (TP)** security incidents is critical for reducing analyst workload and prioritizing responses. The project combines interpretable supervised models with clustering-based adversarial pattern analysis.

---

## Research Questions

1. **Feature Group Impact**
   Which categories of features (Organization, Detection, Entity, Account, Network, Location) contribute most to predicting true positive incidents?

2. **MITRE ATT&CK Technique Patterns**
   Which techniques frequently co-occur in confirmed incidents, and what adversarial patterns emerge?

3. **Temporal Effects**
   How do time-based features affect classification performance across different model families?

---

## Dataset

This project uses the **Microsoft Security Incident Prediction Dataset** (Freitas et al., 2024), the largest publicly available dataset of real-world cybersecurity incidents.

* ~13 million telemetry records
* ~1 million labeled incidents (TP / BP / FP)
* 6,100 organizations
* 441 MITRE ATT&CK techniques
* Collected from production SOC telemetry (Microsoft Defender XDR)

🔗 **Dataset:**
[Microsoft Security Incident Prediction – Kaggle](https://www.kaggle.com/datasets/Microsoft/microsoft-security-incident-prediction/data)

---

## Repository Structure

```text
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
│   ├── TechniquePatternPlotting.py
│
└── Requirements.txt
```

---

## Models Used

### Supervised Learning

* **Decision Tree** — feature importance & interpretability
* **Naïve Bayes** — probabilistic baseline + mutual information
* **Logistic Regression**
* **Linear Support Vector Machine**
* **Multi-Layer Perceptron (ANN)**

### Unsupervised Learning

* **K-Means Clustering** — MITRE ATT&CK technique co-occurrence patterns

  * Risk-weighted using a heuristic informed by MITRE documentation
  * Cluster quality evaluated via silhouette score

---

## Evaluation Metrics

All classification results are evaluated using **macro-averaged** metrics to account for class imbalance:

* Accuracy
* Precision
* Recall
* F1-score

---

## Quick Start

### 1) Installation

```bash
pip install -r Requirements.txt
```

---

### 2) Data Preparation

**Stratified sampling and train/test split**

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
(*expects `all_techniques.csv` in the same folder*):

```bash
python preprocessing/mitre_feature_engineering.py
```

---

### 4) Feature Group Importance (RQ1)

Decision Tree:

```bash
python models/DecisionTree.py
```

Naïve Bayes:

```bash
python models/NaiveBayes.py
```

---

### 5) MITRE Technique Pattern Clustering (RQ2)

```bash
python models/KMeans.py
```

Visualization:

```bash
python -m output.TechniquePatternPlotting
```

---

### 6) Time-Based Feature Experiments (RQ3)

Run all classifiers (ANN / LR / SVM) with and without time features:

```bash
python models/ResultQ3.py
```

---

## Key Findings (Summary)

* Detection and organizational metadata are the strongest predictors of true positives.
* Credential abuse and obfuscation-related MITRE techniques form the highest-risk clusters.
* Time-based features improve Logistic Regression and ANN performance but degrade linear SVM under class imbalance.
* Risk is driven more by *which techniques* are used than by the number of techniques involved.

---

## Reproducibility

* Stratified sampling and cross-validation used throughout
* Deterministic preprocessing
* Identical evaluation protocol across models
* All plots and tables generated programmatically

---

## Authors

* Ella McKercher
* Peiyu Wang
* Yaxi Bai
* Si Yong Lim

School of Computing and Information Systems
The University of Melbourne
