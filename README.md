# DSAI-Lab-T12026-Group-3: GenAI-Powered SQL Refactoring Assistant

## Project Overview

This project develops a **GenAI-powered SQL Refactoring Assistant** that automates the translation of SQL queries from SQLite to Apache Hive (HiveQL).

The system combines **neural machine translation (T5)** with **Retrieval-Augmented Generation (RAG)** to produce syntactically correct and semantically consistent HiveQL queries. The final solution is deployed as an interactive web application and API for real-time usage.

---

## Project Objectives

1. **Accept SQLite Queries**: Process SQL queries written in SQLite dialect  
2. **Generate Equivalent HiveQL**: Produce functionally consistent HiveQL queries  
3. **Ensure Syntax Correctness**: Validate outputs using SQL parsing (sqlglot)  
4. **Real-Time Usability**: Provide an interactive UI and API for query translation  
5. **Extensibility**: Enable adaptation to other SQL dialect pairs  

---

## Key Problem Areas Addressed

- **Dialect Differences**: Handling syntax and function variations between SQLite and HiveQL  
- **Function Mapping**: Translating SQLite-specific functions to Hive equivalents  
- **Query Structure Variations**: Managing joins, subqueries, and aggregations  
- **Syntax Validation**: Ensuring generated queries are executable  

---

## System Architecture

The system follows a **Retrieval-Augmented Translation Pipeline**:
```
SQLite Query
↓
CodeBERT Embedding
↓
FAISS Retrieval (Top-k Similar Examples)
↓
Few-shot Prompt Construction
↓
T5 Model (Fine-tuned)
↓
HiveQL Output
```


---

## Technical Approach

### Model
- **T5-base (220M parameters)**
- Fully fine-tuned on a custom SQLite → HiveQL dataset

### Retrieval (RAG)
- **Embedding Model**: CodeBERT  
- **Vector Store**: FAISS  
- **Retrieval Strategy**: Top-k similar query examples (k = 3)  
- Enhances translation quality via contextual prompting  

### Dataset
- ~600 manually curated seed queries  
- Expanded to ~5,000 using **AST-based augmentation (sqlglot)**  
- Covers joins, aggregations, filters, and subqueries  

---

## Evaluation Strategy

We use a **multi-metric evaluation framework**:

- **Exact Match (EM%)**: Strict correctness after normalization  
- **BLEU Score**: Measures similarity between predicted and reference queries  
- **Parse Validity (PV%)**: Checks syntactic correctness using sqlglot  

This ensures evaluation across:
- Correctness  
- Similarity  
- Executability  

---

## Deployment

### Web Interface
- Built using **Gradio**
- Allows:
  - Query input  
  - RAG toggle  
  - Top-k selection  

### Backend API
- Built using **FastAPI**
- Endpoints:
  - `POST /translate`  
  - `GET /health`  

### Hosting
- Deployed on **Hugging Face Spaces**
- Supports real-time inference  

---

## Performance

- **BLEU Score**: ~0.69  
- **Exact Match**: ~33%  
- **Latency**: ~1–3 seconds per query (T4 GPU)  
- **Cold Start**: ~25 seconds  


---

## Limitations

- Limited handling of highly complex nested queries  
- No schema-aware translation  
- Model size (~900MB) impacts deployment flexibility  

---

## Future Work

- Extend to multiple SQL dialects  
- Introduce schema-aware translation  
- Explore lightweight fine-tuning (LoRA)  
- Improve semantic evaluation using execution-based methods  

---

## Contributors

- **Ajay** – Deployment & Core Pipeline Implementation  
- **Sanjay Rajesh Manwani** – Backend & RAG Integration  
- **Senthilkumar N** – System Integration & Deployment  
- **Madhavan R Mohan** – Implementation, Documentation & Slides  
- **Veral Sharma** – Implementation, Documentation & Slides  

---

## Status

✅ **Project Completed**
