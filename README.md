# Travel Reimbursement Approval Agent

An automated, auditable travel expense reimbursement approval agent following the **"AI extracts, Python decides"** design pattern.

---

## 📌 Overview

This project processes and evaluates enterprise travel reimbursement claims. An LLM parses unstructured expense line items into structured fields, while deterministic Python rules handle all financial calculations, policy limits, and approval routing.

---

## 🏗️ Architecture

```
Claim Input (JSON / Text)
        │
        ▼
[ AI Extraction Layer ]   ──> Extracts category, nights, days, cabin class, ambiguity
        │
        ▼
[ Python Rule Engine ]    ──> Enforces receipts, per-diem caps, ineligible deductions, tiers
        │
        ▼
Structured JSON Decision (APPROVE | PARTIAL_APPROVE | REJECT | MANUAL_REVIEW)
```

- **AI Extracts**: LLM (`ChatNVIDIA`) extracts semantic attributes into a Pydantic schema.
- **Python Decides**: Pure Python rules compute amounts and enforce policy, ensuring zero arithmetic hallucination.

---

## 📋 Policy Rules Summary

| Policy Code                | Category / Rule       | Limit / Action                                                                               |
| -------------------------- | --------------------- | -------------------------------------------------------------------------------------------- |
| **POL-CAT-01**       | Eligible categories   | Airfare (economy), lodging, meals, ground transport, conference fees                         |
| **POL-CAT-02**       | Ineligible categories | Spa, minibar, alcohol, gym, gifts (deducted in full)                                         |
| **POL-PD-01/02/03**  | Per-diem limits       | Meals: \$75/day \| Lodging: \$200/night \| Ground transport: \$50/day                        |
| **POL-AIR-01**       | Airfare cabin         | Economy reimbursable; business/first-class routes to **Manual Review**                 |
| **POL-RCT-01/02**    | Receipts              | Required for items > \$25 and all lodging/airfare; missing routes to **Manual Review** |
| **POL-APR-01/02/03** | Approval tiers        | ≤ \$500 (Auto) \| \$501–\$2,000 (Manager) \| > \$2,000 (Director / Manual Review)          |
| **POL-TIME-01**      | Submission window     | Must be submitted within 30 days of trip end                                                 |

---

## 🚀 Setup & Execution

### 1. Prerequisites

- **Python**: Version `3.10` to `3.13` ([Download Python](https://www.python.org/downloads/))

### 2. Create & Activate Virtual Environment

```bash
# Create environment
python -m venv venv

# Windows (PowerShell):
.\venv\Scripts\activate

# Linux / macOS:
source venv/bin/activate
```

### 3. Install Dependencies & Kernel Support

```bash
pip install ipykernel langchain langchain-nvidia-ai-endpoints pydantic matplotlib
python -m ipykernel install --user --name=venv --display-name="Python (venv)"
```



### 4. NVIDIA API Configuration

Before running the notebook, configure your NVIDIA API key and the model name in the configuration cell of the Jupyter Notebook (`.ipynb`).

1. Replace `YOUR_NVIDIA_API_KEY` with your actual NVIDIA API key.
2. Set `model_name` to the NVIDIA model you want to use.



### 5. Select Kernel & Run Notebook

1. Open [`harshpande.ipynb`](yourname.ipynb) in **VS Code** or **Jupyter**.
2. Click **Select Kernel** in the top-right corner (or **Kernel → Change Kernel** in Jupyter).
3. Select your environment: **`Python (venv)`** (or `.venv`).
4. Click **Run All** to execute all cells.

---

## 📊 Outputs & Artifacts

- **Console Output**: Structured JSON decision for each claim with decision status, approved/deducted amounts, policy references, and audit explanations.
- **[`results.json`](results.json)**: Serialized evaluation results for all processed claims.
- **[`UI SS_1.png`](UI SS_1.png)**: Visual summary dashboard featuring a decision breakdown donut chart and an approved vs. deducted amounts bar chart.
