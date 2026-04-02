# Milestone 5 Report

## 1. Overview
For Milestone 5, our primary objective was to perform a **comprehensive evaluation and analysis** of the SQL refactoring system developed in previous milestones.

Unlike earlier milestones focused on model building and optimization, this phase focuses on:
* Evaluating performance using a **full metric suite**
* Comparing multiple **pipeline variants (Baseline → RAG → FSP → CoT → Full)**
* Analyzing **retrieval quality, reasoning patterns, and complexity-wise performance**

---

## 2. Evaluation Setup

We loaded the **best-performing model from Milestone 4**:

* Best Experiment: `LoRA-BestConfig`
* Pipeline: `Full (RAG + FSP + CoT)`
* LoRA enabled for efficient inference

**Prediction Generation**
* Batched inference used for scalability
* Beam search applied (`num_beams = 4`)
* Predictions generated for all pipeline variants:
  - Baseline
  - RAG
  - Few-Shot Prompting (FSP)
  - Chain-of-Thought (CoT)
  - Full Pipeline

---

## 3. Full Metric Suite

We evaluated each pipeline using **five complementary metrics**:

1. **Exact Match (EM%)**: Measures exact query match with ground truth  

2. **BLEU Score**: Measures n-gram similarity  

3. **Parse Validity (PV%)**: Checks if generated SQL is syntactically valid  

4. **Token-Level F1 (TF1%)**: Measures token overlap between prediction and reference  

5. **ROUGE-L (RL%)**: Measures longest common subsequence similarity  

**Observation:**
Using multiple metrics provided a **balanced evaluation** across:
* Structural correctness
* Semantic similarity
* Syntax validity  

### 3.1 Training Dynamics and Hyperparameter Insights

We analyzed training logs from multiple experiments (including weight decay and dropout variations) to understand model behavior across epochs.

### 3.2 Training Stability and Convergence

From the training logs:

**Experiment (Weight Decay = 0.0):**
* Training Loss reduced significantly:  
  - Epoch 1: ~6.31 → Epoch 5: ~0.12  
* Validation Loss stabilized around ~0.02  

**Observation:**
The model converges rapidly within the first few epochs.

**Inference:**
The dataset is relatively well-structured, allowing the model to learn SQL transformations quickly.

### 3.3 Exact Match vs BLEU Behavior

Across epochs:
* Exact Match fluctuates between **81% → 87% → 83% → 85%**
* BLEU remains consistently high (**~98–99%**)

**Observation:**
* BLEU is very high even when Exact Match drops  
* Exact Match is more sensitive to small structural differences  

**Inference:**
High BLEU but lower Exact Match indicates:
> The model generates **structurally similar but not identical SQL queries**

This highlights the importance of using **multiple evaluation metrics**.

### 3.4 Parse Validity Issue

Across all epochs:
* Parse Validity = **0.0**

**Observation:**
Despite high BLEU and Exact Match, parse validity remains zero.

**Inference:**
This suggests:
* Either parsing logic is too strict  
* Or generated queries contain minor syntax inconsistencies  

**Key Insight:**
> Surface-level similarity (BLEU) does not guarantee executable SQL correctness  

### 3.5 Impact of Dropout (Regularization)

From Dropout Experiment:

* Exact Match improved up to **~92%**
* Training loss decreased more smoothly compared to baseline  

**Observation:**
Dropout improves generalization and prevents overfitting.

**Inference:**
Regularization helps the model:
* Avoid memorization  
* Produce more robust outputs  

### 3.6 Overfitting Patterns

**Observation:**
* Training loss continuously decreases  
* Validation loss stabilizes early  

**Inference:**
The model does not heavily overfit, but:
* Gains plateau after early epochs  
* Additional training provides diminishing returns  

### 3.7 Key Experimental Insights (From Logs)

Based on training outputs:

* Rapid convergence indicates strong dataset quality  
* BLEU alone is not sufficient for evaluation  
* Exact Match is a stricter and more reliable metric  
* Parse validity exposes hidden correctness issues  
* Regularization (dropout) improves robustness  
* Early stopping could improve efficiency  

### 3.8 Conclusion

These training-level insights reinforce that:

> Model performance should be evaluated across **multiple dimensions**:
* Surface similarity (BLEU)
* Structural correctness (Exact Match)
* Executability (Parse Validity)

This multi-metric approach ensures a **more reliable and production-ready SQL refactoring system**.

---

## 4. Pipeline Comparison

We evaluated five pipeline variants:

| Pipeline | Description |
|----------|------------|
| Baseline | No RAG, no prompting |
| RAG | Retrieval-augmented context |
| FSP | Few-shot prompting with examples |
| CoT | Chain-of-thought reasoning |
| Full | RAG + FSP + CoT |

**Observation:**
* Baseline → lowest performance (no context)
* RAG → significant improvement (context injection)
* FSP → further improvement (example-based learning)
* CoT → improved reasoning but inconsistent gains
* Full → best overall performance

**Inference:**
Performance improves as we move from:
> **No Context → Context → Examples → Reasoning → Combined System**

---

## 5. Few-Shot Prompting (FSP) Analysis

### 5.1 Token Budget Breakdown
We analyzed how input tokens are distributed:

* Query tokens  
* Retrieved context  
* Few-shot examples  

**Observation:**
* Few-shot examples consume a significant portion of token budget  

**Inference:**
There is a trade-off between:
* More examples → better guidance  
* More tokens → risk of truncation  

### 5.2 Example Diversity

We measured **clause overlap** between:
* Input query  
* Retrieved examples  

(SQL clauses like SELECT, WHERE, GROUP BY, JOIN)

**Observation:**
* High overlap → better performance  
* Low overlap → weaker guidance  

**Inference:**
Few-shot effectiveness depends on **relevance of examples**, not just quantity  

### 5.3 FSP Contribution

We measured **performance lift of FSP over RAG** across query complexity levels.

**Observation:**
* FSP provides higher gains for complex queries  
* Minimal impact on simple queries  

**Inference:**
Few-shot prompting is more useful when:
> Query structure is complex and requires pattern guidance  

---

## 6. Chain-of-Thought (CoT) Analysis

### 6.1 CoT Strip Verification

We ensured that reasoning traces are:
* Removed before evaluation  
* Do not affect final metrics  

**Observation:**
Stripping worked correctly — no leakage into final outputs  

### 6.2 CoT Step Correctness

We evaluated whether reasoning steps correctly mention:
* SQL dialect differences  
* Functions and transformations  

**Observation:**
* Many CoT steps correctly identify transformations  
* Some include redundant or generic reasoning  

**Inference:**
CoT improves interpretability but not always precision  

### 6.3 CoT vs Accuracy

We compared:
* Predictions with CoT reasoning  
* Predictions without CoT  

**Observation:**
* CoT improves accuracy in some cases  
* Inconsistent gains overall  

**Inference:**
CoT is helpful but:
> Not a guaranteed performance booster  

---

## 7. Retrieval Quality Metrics

We evaluated retrieval quality using a **heuristic relevance score**:

* Overlap between query and retrieved chunks  
* Removal of stopwords  
* Focus on SQL-specific tokens  

**Observation:**
* Higher relevance → better final predictions  
* Irrelevant chunks degrade performance  

**Inference:**
Retrieval quality is a **critical bottleneck** in RAG systems  

---

## 8. Per-Complexity Performance Analysis

We analyzed performance across query complexity levels:

* Simple  
* Medium  
* Complex  

**Observation:**
* Baseline struggles with complex queries  
* RAG improves performance across all levels  
* FSP and Full pipeline show strongest gains for complex queries  

**Inference:**
Advanced techniques (RAG + FSP + CoT) are most beneficial for:
> **High-complexity SQL transformations**

---

## 9. Visualization and Comparative Analysis

We generated:
* Grouped bar charts comparing all pipelines across metrics  
* Complexity-wise performance breakdown  

**Observation:**
* Full pipeline consistently outperforms others  
* Metric trends align across evaluation methods  

---

## 10. Key Learnings

* Baseline models lack contextual understanding  
* RAG provides the largest performance improvement  
* Few-shot prompting enhances pattern learning  
* CoT improves reasoning but is inconsistent  
* Retrieval quality directly impacts final outputs  

---

## 11. Final Conclusion

The best-performing system is:

* **T5 + LoRA (fine-tuned)**
* **RAG (retrieval-based context)**
* **Few-shot prompting (example guidance)**
* **Optional CoT reasoning**

**Final Insight:**
> Performance scales with **context quality + example relevance + reasoning alignment**

---

## 12. Future Work

* Improve retrieval relevance using learned retrievers  
* Optimize token usage in few-shot prompting  
* Filter noisy CoT reasoning steps  
* Introduce execution-based evaluation metrics  
* Deploy as an interactive SQL refactoring assistant  

---

### Presented By Group 3

**AJAY (21f1005414) \
MADHAVAN R MOHAN (22f3000983) \
SANJAY RAJESH MANWANI (21f3002914) \
SENTHILKUMAR N (21f1006434) \
VERAL SHARMA (22f1001101)**
