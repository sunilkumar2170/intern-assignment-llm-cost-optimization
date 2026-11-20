# 🚀 ShopAssist — Intelligent Support Copilot & LLM Cost Optimization

<p align="center">
  <strong>Building Cost-Efficient AI Agents for E-Commerce Customer Support</strong>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Python-3.x-blue?logo=python" />
  <img src="https://img.shields.io/badge/LLM-Cost%20Optimization-purple" />
  <img src="https://img.shields.io/badge/Architecture-AI%20Agents-orange" />
  <img src="https://img.shields.io/badge/Testing-Pytest-green?logo=pytest" />
</p>

---

## 📌 01. Project Overview

**ShopAssist** is an AI-powered Tier-1 customer support copilot designed for an e-commerce platform.

It processes customer conversations, retrieves relevant order information, determines the appropriate resolution, and provides supporting help-center references.

The primary objective of this project is to **minimize LLM token consumption and API costs without compromising response correctness.**

This assignment explores two approaches:

- **Q1 — Single-Call Optimization:** Improving prompt efficiency and reducing unnecessary token usage.
- **Q2 — Multi-Stage Agent Pipeline:** Separating deterministic processing from LLM-based response generation.

---

## 🎯 02. Problem Statement

LLM-powered customer support systems can become expensive when they repeatedly process large prompts, unnecessary context, and lengthy outputs.

ShopAssist addresses this problem by optimizing how information is prepared, retrieved, processed, and passed to the LLM.

For every customer ticket, the system:

1. Reads the customer conversation.
2. Retrieves relevant order information.
3. Determines the appropriate support action.
4. Identifies the supporting help-center article.

### Supported Support Actions

| Action | Description |
|---|---|
| `REFUND` | Initiate a refund |
| `REPLACE` | Replace the purchased item |
| `SEND_ARTICLE` | Share a relevant help-center article |
| `ESCALATE_TIER2` | Escalate the issue to Tier-2 support |

---

## 🏗️ 03. System Architecture & Implementation

The project contains two independent optimization tracks.

### 🔹 Q1 — Single-Call LLM Optimization

**File:** `question1/agent.py`

The first approach focuses on reducing the cost of a single LLM interaction.

**Key Optimizations:**

- Removed unnecessary help-center content from the prompt.
- Replaced verbose instructions with concise prompts.
- Used a compact JSON order snapshot.
- Eliminated unnecessary prompt padding.
- Evaluated prompt caching against uncached input.
- Reduced unnecessary output generation.

**Core Idea:** Provide the LLM with only the information required to make the correct support decision.

### 🔹 Q2 — Multi-Stage Agent Pipeline

**File:** `question2/agent.py`

The second approach introduces a structured four-stage processing pipeline.

| Stage | Component | Responsibility |
|---|---|---|
| 01 | Triage | Processes customer tickets and order information using Python |
| 02 | Retrieve | Selects the most relevant help-center article |
| 03 | Compose | Generates the response using the LLM |
| 04 | Audit | Validates the output structure, action, and article reference |

**Core Idea:** Use deterministic Python logic wherever possible and reserve LLM processing for tasks that require language generation.

---

## 📊 04. Performance & Cost Optimization Results

The following results were obtained across the four public tickets.

| Performance Metric | Q1 | Q2 |
|---|---:|---:|
| Baseline Cost | 5,144 | 6,320 |
| Optimized Cost | 3,870 | 4,275 |
| Points Saved | 1,274 | 2,045 |
| Cost Reduction | 24.77% | 32.36% |

### Cost Reduction Comparison

- **Q1:** Achieved a 24.77% reduction in total billing points.
- **Q2:** Achieved a 32.36% reduction in total billing points.

<details>
<summary><strong>View cost comparison details</strong></summary>

| Track | Original | Optimized | Savings |
|---|---:|---:|---:|
| Question 1 | 5,144 | 3,870 | 1,274 |
| Question 2 | 6,320 | 4,275 | 2,045 |

</details>

---

## 💰 05. LLM Billing & Token Economics

The project uses a token-based billing model through the provided local `LlmClient`.

| Token Category | Cost per Token |
|---|---:|
| Uncached Input | 3 points |
| Cached Input | 0.3 points |
| Output | 15 points |

### Billing Formula

```text
Total Cost =
    (Uncached Input × 3)
  + (Cached Input × 0.3)
  + (Output × 15)
```

**Prompt Caching:** An identical prefix of at least 1,024 tokens across calls qualifies as cached input.

Detailed cost accounting is implemented through `CostSession` in `shared/billing.py`.

---

## 🧪 06. Testing, Validation & Reliability

Both implementations were tested using the provided test suites.

| Test Suite | Tests Passed | Status |
|---|---:|---|
| Question 1 | 3/3 | Passed |
| Question 2 | 6/6 | Passed |
| **Total** | **9/9** | **Passed** |

### Additional Validation

An additional unseen ticket, `T-1091`, was tested to evaluate behavior beyond the four public examples.

The objective was to verify that the optimized implementation could handle an additional ticket while maintaining the expected decision and cost constraints.

---

## 🛠️ 07. Technology Stack

| Technology | Purpose |
|---|---|
| Python | Core implementation |
| LLM Client | Language-model interaction |
| JSON | Structured order information and responses |
| Pytest | Automated testing |
| Prompt Engineering | Prompt and token optimization |
| Agent Pipeline | Multi-stage task orchestration |

---

## 📂 08. Project Structure

```text
intern-assignment-llm-cost-optimization/
│
├── question1/
│   ├── agent.py
│   └── run.py
│
├── question2/
│   ├── agent.py
│   └── run.py
│
├── questions/
│   ├── question-1.md
│   └── question-2.md
│
├── data/
│   ├── tickets/
│   ├── orders/
│   └── kb/
│
├── shared/
│   ├── llm.py
│   ├── tools.py
│   └── billing.py
│
├── tests/
│
├── requirements.txt
├── README.md
└── WRITEUP.md
```

---

## ⚙️ 09. Installation & Execution

### Step 1 — Clone the Repository

```bash
git clone https://github.com/sunilkumar2170/intern-assignment-llm-cost-optimization.git

cd intern-assignment-llm-cost-optimization
```

### Step 2 — Install Dependencies

```bash
pip install -r requirements.txt
```

### Step 3 — Execute Question 1

```bash
python question1/run.py
```

### Step 4 — Execute Question 2

```bash
python question2/run.py
```

### Step 5 — Run Automated Tests

```bash
pytest -q
```

To execute individual test suites:

```bash
python -m pytest -q tests/test_question1.py

python -m pytest -q tests/test_question2.py
```

---

## 📄 10. Implementation Notes

- `shared/llm.py` provides the local `LlmClient` and billing simulation.
- `shared/tools.py` provides access to order information.
- `shared/billing.py` handles token-based cost accounting.
- Agent implementations must not import `eval/reference.py`.
- The provided shared infrastructure and test files are not modified.

---

## 🏁 11. Conclusion

ShopAssist demonstrates how thoughtful prompt engineering and efficient agent architecture can reduce the operational costs of LLM-powered applications.

Through two different optimization strategies, this project achieves:

- Reduced prompt and output token consumption.
- More efficient context construction.
- Targeted knowledge retrieval.
- Deterministic processing using Python.
- Structured response validation.
- Lower overall LLM billing costs.

**The key takeaway:** Building efficient AI agents is not just about choosing a powerful LLM; it is also about minimizing unnecessary work and using the model only where it adds value.

---

<p align="center">
  <strong>ShopAssist — Making AI-Powered Customer Support More Cost-Efficient.</strong>
</p>