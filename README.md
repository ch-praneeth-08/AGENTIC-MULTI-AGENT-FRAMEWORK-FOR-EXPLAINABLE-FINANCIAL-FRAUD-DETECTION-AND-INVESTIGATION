# Agentic Multi-Agent Framework for Explainable Fraud Detection

Machine learning, graph neural networks and an agentic architecture over the
**IEEE-CIS (Vesta)** fraud dataset.

**Milestone status: P0–P4 complete.** P5–P11 (ensemble, XAI, evidence, investigation,
reporting, coordinator) are specified but **not implemented**.

| Model | Holdout PR-AUC | Holdout ROC-AUC | Acceptance |
|---|---|---|---|
| **XGBoost (full)** | **0.6429** | **0.9404** | ✅ met |
| LightGBM (full) | 0.6240 | 0.9356 | ✅ met |
| GNN (RelationalSAGE) | 0.4143 | 0.8500 | ❌ 0.86 bar missed |

At a 1% alert budget the best model reviews 1,057 transactions and finds **1,002 real frauds**
— precision 0.948, a **27.7× lift** over random review.

---

## Quick start

```bash
python -m pip install -r requirements.txt
```

```bash
python -m pip install torch==2.11.0 --index-url https://download.pytorch.org/whl/cu128
```

```bash
python -m pip install torch-geometric mlflow
```

Place the four Kaggle CSVs in `data/raw/`, then run the pipeline (under 5 minutes total):

```bash
python -m src.main --agent preprocessing
```

```bash
python -m src.main --agent feature_engineering
```

```bash
python -m src.main --agent detection
```

```bash
python -m src.main --agent gnn
```

```bash
python -m src.main --agent evaluation
```

### Verify

```bash
python -m src.validation.leakage_audit
```

```bash
python -m src.validation.causality_test
```

### View

```bash
streamlit run app/dashboard.py
```

```bash
mlflow ui --backend-store-uri sqlite:///mlflow.db
```

---

## What makes this project defensible

**Temporal integrity is enforced, not assumed.** The split boundaries *and* the exact expected
row and fraud counts are frozen in `config/config.yaml` and asserted on every run. Two
automated gates — 23 leakage checks and 19 causality checks — must pass before any result is
believed. The strongest of them rebuilds every causal feature from a dataset that ends before
the test period exists and verifies all **590,540 labelled rows come out identical**.

**The strongest feature is deliberately weakened.** Account fraud history gives a 34× lift, but
a chargeback takes weeks to arrive, so a fraud from yesterday is not knowable today. A 30-day
label delay cuts it to 16.4× — half the signal surrendered for a number the deployed system
could actually reproduce.

**Negative results are reported.** The GNN missed its accuracy bar and contributes nothing to
an ensemble. Velocity features turned out to be nearly worthless. Class-imbalance weighting
made things worse. All measured, all documented.

---

## Documentation

| Document | Covers |
|---|---|
| [`COMPLETE_PROJECT_DOCUMENTATION.md`](docs/COMPLETE_PROJECT_DOCUMENTATION.md) | Architecture, dataset, workflow, status |
| [`IMPLEMENTATION_STATUS_AUDIT.md`](docs/IMPLEMENTATION_STATUS_AUDIT.md) | What is built, executed and verified, with evidence |
| [`PREPROCESSING_DEEP_DIVE.md`](docs/PREPROCESSING_DEEP_DIVE.md) | Every P1 operation and why |
| [`FEATURE_ENGINEERING_DEEP_DIVE.md`](docs/FEATURE_ENGINEERING_DEEP_DIVE.md) | Every feature and its leakage argument |
| [`ML_TRAINING_DEEP_DIVE.md`](docs/ML_TRAINING_DEEP_DIVE.md) | How gradient boosting works and how we used it |
| [`GNN_DEEP_DIVE.md`](docs/GNN_DEEP_DIVE.md) | Graph construction, message passing, results |
| [`MODEL_EVALUATION_AND_RESULTS.md`](docs/MODEL_EVALUATION_AND_RESULTS.md) | Methodology, metrics, interpretation |
| [`CODEBASE_WALKTHROUGH.md`](docs/CODEBASE_WALKTHROUGH.md) | Every file, class and function |
| [`GUIDE_QUESTION_BANK.md`](docs/GUIDE_QUESTION_BANK.md) | 137 questions with answers |
| [`MILESTONE_P0_P4_COMPLETION_REPORT.md`](docs/MILESTONE_P0_P4_COMPLETION_REPORT.md) | What passed, what failed, how to reproduce |
| [`DEMO_WALKTHROUGH.md`](docs/DEMO_WALKTHROUGH.md) | 12–15 minute demonstration script |

Per-phase specs written *before* each phase: [`00_PROJECT_CHARTER.md`](docs/00_PROJECT_CHARTER.md)
· [`01_PREPROCESSING_SPEC.md`](docs/01_PREPROCESSING_SPEC.md)
· [`02_FEATURE_ENGINEERING_SPEC.md`](docs/02_FEATURE_ENGINEERING_SPEC.md)
· [`03_DETECTION_SPEC.md`](docs/03_DETECTION_SPEC.md)
· [`04_GNN_SPEC.md`](docs/04_GNN_SPEC.md)

---

## Requirements

Python 3.10 · 16 GB RAM (peak usage ~2 GB) · CUDA GPU recommended for the GNN (CPU fallback
works). Developed on Windows 11 with an RTX 5070 Ti Laptop (12.8 GB).
"# AGENTIC-MULTI-AGENT-FRAMEWORK-FOR-EXPLAINABLE-FINANCIAL-FRAUD-DETECTION-AND-INVESTIGATION" 
