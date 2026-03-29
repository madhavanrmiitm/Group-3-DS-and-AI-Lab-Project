# Milestone 4 Report

## Overview
For Milestone 4, our primary objective was to enhance the SQL refactoring assistant by introducing **Retrieval-Augmented Generation (RAG)** and conducting **systematic experiments** to improve model performance.

Unlike Milestone 3, which focused on validating the base T5 pipeline, this milestone focuses on:
* Incorporating **external knowledge (SQL docs + schema)** using RAG
* Improving **context-awareness in query refactoring**
* Running **multiple controlled experiments (1–9)** to optimize performance

---

## RAG Pipeline Design

To overcome the limitations of a standalone LLM, we implemented a **3-step RAG pipeline**:

1. Knowledge Base Creation  
2. Embedding + Vector Store  
3. Retrieval + Reranking  

This allows the model to generate outputs using **both learned knowledge and retrieved context**.

---

## RAG Step 1: Knowledge Base Creation

We constructed a domain-specific knowledge base using:

* SQLite documentation  
* HiveQL documentation  
* Database schema information  

**Processing Steps:**
* Cleaned and normalized text
* Chunked into smaller segments for retrieval
* Stored as independent knowledge units

**Observation:**
Some documents were unavailable during execution, so fallback placeholders were used.

**Inference:**
The completeness and quality of the knowledge base directly influence the final model outputs.

---

## RAG Step 2: Embeddings and Vector Store

To enable semantic retrieval, we converted all knowledge chunks into vector representations.

**Implementation:**
* Embedding Model: `all-MiniLM-L6-v2`
* Vector Store: FAISS (for efficient similarity search)

**Process:**
* Each chunk → embedding vector  
* Stored in FAISS index  

**Observation:**
Embedding generation and indexing were efficient and scalable.

**Inference:**
Vector-based retrieval allows the system to fetch **semantically similar SQL patterns**, not just keyword matches.

---

## RAG Step 3: Retriever and Reranker

We implemented a two-stage retrieval system:

**1. Retriever (FAISS):**
* Fetches top-k relevant chunks quickly  

**2. Reranker (Cross-Encoder):**
* Model: `ms-marco-MiniLM-L-6-v2`
* Reorders retrieved results based on relevance  

**Observation:**
Reranking significantly improved the relevance of retrieved context.

**Inference:**
Higher-quality context leads to more accurate and structured SQL refactoring.

---

## RAG Dataset Construction

We created two versions of the dataset:

1. **Baseline Dataset (No RAG)**
2. **RAG-Augmented Dataset**

**Pipeline:**
* Input SQL query  
* Retrieve top-k chunks  
* Apply reranking  
* Construct context string  
* Append context to input  

**Observation:**
Dataset creation with RAG was computationally expensive due to per-query retrieval.

**Inference:**
There is a trade-off between **context quality and preprocessing time**.

---

## Experimental Strategy

We conducted **9 experiments** to systematically improve performance.

**Approach:**
* Start with baseline model  
* Modify one parameter at a time  
* Evaluate using validation metrics  

**Goal:**
Identify the best combination of:
* Model configuration  
* Training parameters  
* Retrieval strategy  

---

### Experiment 1: Baseline Model

**Setup:**
* Model: T5-base with LoRA
* No RAG
* Default hyperparameters  

**Observation:**
* Total parameters ≈ 223M  
* Trainable parameters ≈ 0.39%  

**Inference:**
LoRA enables efficient fine-tuning, but the model lacks contextual awareness.

---

### Experiment 2: Learning Rate Tuning

**Changes:**
* Tested multiple learning rates  

**Observation:**
* High learning rate → unstable training  
* Low learning rate → slow convergence  

**Inference:**
Selecting an optimal learning rate is critical for stable and efficient training.

---

### Experiment 3: Scheduler Variants

**Schedulers Tested:**
* Linear  
* Cosine  
* Constant  

**Observation:**
Cosine scheduler provided smoother convergence behavior.

**Inference:**
Learning rate scheduling significantly affects training stability.

---

### Experiment 4: Beam Search Optimization

**Tested Beam Sizes:**
* 2, 4, 6, 8  

**Observation:**
* Higher beam size → better output quality  
* Increased inference time  

**Inference:**
There is a trade-off between **accuracy and latency**.

---

### Experiment 5: Optimizer Comparison

**Optimizers Tested:**
* AdamW  
* Adafactor  

**Observation:**
* AdamW → more stable training  
* Adafactor → memory efficient  

**Inference:**
Optimizer choice depends on resource constraints and stability requirements.

---

### Experiment 6: Regularization Techniques

**Techniques Applied:**
* Weight decay  
* Dropout  
* Label smoothing  

**Observation:**
* Reduced overfitting  
* Improved generalization  

**Inference:**
Regularization improves robustness of the model.

---

### Experiment 7: RAG Integration

**Change Introduced:**
* Added retrieved context to model input  

**Setup:**
* Top-3 retrieved + reranked chunks  
* Lower learning rate for stability  

**Observation:**
Outputs became more structured and context-aware.

**Inference:**
RAG significantly enhances semantic understanding of SQL queries.

---

### Experiment 8: RAG Ablation Study

**Compared:**
* Retriever only  
* Retriever + Reranker  

**Observation:**
Reranker improved relevance of retrieved context.

**Inference:**
Retrieval quality is more important than quantity.

---

### Experiment 9: Final Optimized Model

**Best Configuration:**
* Optimal learning rate  
* Best scheduler  
* Regularization applied  
* RAG + reranker enabled  

**Goal:**
Maximize validation performance  

---

## Final Results and Evaluation

**Evaluation Metric:**
* Validation Exact Match  

**Output:**
* Best model selected based on performance  
* Results stored in: `milestone4_experiment_log.csv`  

**Observation:**
RAG-based models consistently outperformed baseline models.

---

## Key Learnings

* Baseline model lacks contextual understanding  
* Hyperparameter tuning improves training stability  
* RAG provides significant improvement in output relevance  
* Reranker is critical for high-quality retrieval  

---

## Conclusion

The best-performing system combines:

* T5 + LoRA fine-tuning  
* Retrieval-Augmented Generation  
* Cross-encoder reranking  

**Final Insight:**
Model performance depends on:
> **Model + Data + Retrieval Quality**

---

## Future Work

* Expand knowledge base coverage  
* Use more advanced embedding models  
* Enable real-time query evaluation  
* Deploy as an interactive developer tool  

---

### Presented By Group 3

**AJAY (21f1005414) \
MADHAVAN R MOHAN (22f3000983) \
SANJAY RAJESH MANWANI (21f3002914) \
SENTHILKUMAR N (21f1006434) \
VERAL SHARMA (22f1001101)**
