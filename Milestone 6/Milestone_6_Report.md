# Milestone 6 Report  
## GenAI Powered SQL Refactoring Assistant

---

## 1. Overview

Milestone 6 focuses on building a **fully integrated, optimized SQL refactoring pipeline** that translates **SQLite queries into HiveQL** using a combination of:

- Transformer-based sequence-to-sequence modeling (T5)
- Retrieval-Augmented Generation (RAG)
- Few-Shot Prompting (FSP)
- Chain-of-Thought reasoning (CoT)
- Parameter-efficient fine-tuning (LoRA)

This milestone extends previous work by:
- Integrating all components into a **single unified pipeline**
- Adding **advanced retrieval (HyDE)**
- Optimizing **memory usage for T4 GPU constraints**
- Running **systematic experiments (baseline → full pipeline → LoRA)**

---

## 2. Dataset

### 2.1 Source
- PARROT dataset (SQLite ↔ HiveQL pairs)

### 2.2 Splits
| Split | Size |
|------|------|
| Train | 3064 |
| Validation | 963 |
| Test | 888 |

### 2.3 Complexity Distribution (Train)
| Complexity | Count |
|-----------|------|
| High | 1607 |
| Medium | 844 |
| Low | 613 |

---

## 3. Model Configuration

### 3.1 Base Model
- Model: **T5-base**
- Parameters: ~222M
- Architecture: Encoder-Decoder Transformer

### 3.2 Input/Output Design

Custom prefixes used:
- `<sqlite>` → input query
- `<hive>` → target query
- `<context>` → retrieved documents (RAG)
- `<examples>` → few-shot examples (FSP)
- `<sep>` → chunk separator

### 3.3 Tokenization

- Tokenizer: `T5Tokenizer`
- Special tokens added: 6
- Vocabulary size: ~32,106

---

## 4. Training Configuration

| Parameter | Value |
|----------|------|
| Epochs | 10 |
| Batch size | 4 |
| Gradient Accumulation | 8 |
| Effective Batch Size | 32 |
| Learning Rate | 3e-4 |
| Scheduler | Cosine |
| Warmup Ratio | 0.06 |
| Weight Decay | 0.01 |
| FP16 | Enabled |

---

## 5. Memory Optimization

Training was performed on a **T4 GPU (15GB)**.

### Techniques Used:
- Gradient accumulation
- FP16 precision
- CPU offloading (RAG components)
- Optional LoRA training
- CUDA memory fragmentation control

### Outcome:
- Stable training without OOM errors
- Efficient utilization of limited GPU memory

---

## 6. Baseline Pipeline

### Preprocessing

Baseline input:
```<sqlite> SELECT ...```

Target:
<hive> SELECT ...


### Characteristics:
- No RAG
- No FSP
- No CoT

Used as a **control experiment**.

---

## 7. Evaluation Metrics

### 7.1 Exact Match (EM)
- Measures exact string match between prediction and ground truth

### 7.2 BLEU Score
- Measures n-gram similarity

### 7.3 Parse Validity
- Uses `sqlglot` to verify HiveQL syntax correctness

---

## 8. RAG Pipeline

### 8.1 Step 1: Knowledge Base Construction

Sources:
- SQLite documentation (web scraped)
- HiveQL documentation (web scraped)
- Schema derived from dataset

### Chunk Statistics:
| Source | Chunks |
|--------|-------|
| SQLite Docs | 295 |
| HiveQL Docs | 359 |
| Schema | 2 |
| **Total** | **656** |

---

### 8.2 Chunking Strategy

- Chunk size: 150 tokens
- Overlap: 30 tokens

Purpose:
- Preserve context
- Improve retrieval quality

---

### 8.3 Embedding

- Model: `CodeBERT`
- Dimension: 768
- Device: CPU

Reason:
- Saves GPU memory (~400MB)
- Acceptable latency for offline indexing

---

### 8.4 FAISS Index

- Index type: Inner Product (cosine similarity)
- Vectors stored: 656

Used for fast retrieval of relevant chunks

---

## 9. Retrieval & Reranking

### 9.1 Retrieval

- Bi-encoder retrieves top-k candidates

### 9.2 Reranking

- Cross-encoder re-ranks candidates
- Selects top-n relevant chunks

---

## 10. HyDE (Hypothetical Document Embedding)

### Problem:
- SQLite and HiveQL use different vocabularies

### Solution:
1. Generate hypothetical HiveQL query using T5
2. Use it as retrieval query
3. Retrieve more relevant documents

### Benefit:
- Reduces vocabulary mismatch
- Improves RAG effectiveness

---

## 11. Few-Shot Prompting (FSP)

- Dynamically retrieves **1 similar example**
- Injected into prompt

Format:
```<examples> SQLite → HiveQL example```


### Benefit:
- Improves generalization
- Helps model learn patterns dynamically

---

## 12. Chain-of-Thought (CoT)

- Applied to **30% of training samples**

Format:
```
Translate step-by-step:
...
Answer: final query
```


### Benefit:
- Improves reasoning for complex queries

---

## 13. Final Prompt Structure
```
<sqlite> query
<context> retrieved chunks
<examples> similar example
Translate step-by-step:
```


---

## 14. Experiments

### 14.1 Core Experiments (1–12)

- Baseline
- +RAG
- +FSP
- +CoT
- Full pipeline

### 14.2 LoRA Experiments (13–17)

- LoRA baseline
- LoRA + RAG
- LoRA + full pipeline
- Rank ablation

---

## 15. LoRA (Parameter Efficient Training)

### Configuration:
| Parameter | Value |
|----------|------|
| Rank (r) | 16 |
| Alpha | 32 |
| Dropout | 0.1 |

### Target Modules:
- Query (q)
- Value (v)

### Benefits:
- Trains ~1% of parameters
- Saves ~2.7GB memory
- Faster training

---

## 16. System Strengths

- Modular architecture
- Efficient memory usage
- Strong retrieval augmentation
- Handles complex SQL queries
- Scalable pipeline

---

## 17. Challenges

- Long input sequences (512 tokens)
- Retrieval latency
- Vocabulary mismatch
- Limited GPU memory

---

## 18. Key Innovations

- HyDE-based retrieval
- Schema-aware RAG
- Dynamic few-shot prompting
- Memory-efficient training with LoRA

---

## 19. Conclusion

Milestone 6 delivers a **fully integrated SQL refactoring system** combining:

- RAG
- FSP
- CoT
- LoRA

### Outcome:
- Improved translation accuracy
- Better handling of complex queries
- Efficient training under constraints

---

### Presented By Group 3

**AJAY (21f1005414) \
MADHAVAN R MOHAN (22f3000983) \
SANJAY RAJESH MANWANI (21f3002914) \
SENTHILKUMAR N (21f1006434) \
VERAL SHARMA (22f1001101)**
