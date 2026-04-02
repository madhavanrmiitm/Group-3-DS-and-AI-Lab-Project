# Project Worklog - DSAI Lab T1 2026 Group 3

## Project Overview
- **Repository**: Group-3-DS-and-AI-Lab-Project
- **Group Number, Term**: Group 3, Jan 2026 Term
- **Status**: In Progress

## Tasks

### Initial Setup (Completed)
- [x] Repository created and initialized
- [x] README.md created with project title
- [x] Problem Statement Document added

### Milestone 1 (Completed)
- [x] Milestone 1 directory created
- [x] Detailed milestone requirements documented
- [x] Subtasks and deliverables submitted

### Milestone 2 (Completed)
- [x] Milestone 2 directory created
- [x] Detailed milestone requirements documented
- [x] Subtasks and deliverables submitted
- [x] Primary dataset identified (PARROT benchmark, weizhoudb/PARROT)
- [x] Supplementary dataset identified (Spider benchmark) for augmentation
- [x] Dataset ownership, format, and usage constraints documented
- [x] Dataset size and feature distribution described
- [x] Quality assessment strategy defined (dedup, SQL parsing, encoding checks)
- [x] Adequacy evaluation conducted; augmentation strategy designed
- [x] Train / validation / test split strategy defined with stratification
- [x] Data leakage prevention measures specified
- [x] Instruction-response formatting for LLM fine-tuning defined
- [x] RAG document corpus and chunking strategy defined
- [x] Vector database options explored; ChromaDB selected for development
- [x] Preprocessing pipeline documented for full reproducibility

### Milestone 3 (Completed)
- [x] Milestone 3 directory created
- [x] Detailed milestone requirements documented
- [x] Dataset organization and project directory structure defined
- [x] Preprocessing and Tokenization logic implemented (T5-Tokenizer, 128 MAX_LEN)
- [x] Model architecture selected and justified (T5 Encoder-Decoder)
- [x] Data flow diagram from raw input to HiveQL output mapped
- [x] Expected input formats and tensor shapes verified ([Batch_Size, 128])
- [x] Small-scale end-to-end pipeline implemented using a 10-sample subset
- [x] Model inference verified and initial similarity metrics calculated (~0.78)
- [x] Milestone 3 Report and Contributions document finalized
- [x] Presentation slides for Milestone 3 prepared
- [x] Subtasks and deliverables submitted

### Milestone 4 (Completed)
- [x] Milestone 4 directory created
- [x] RAG pipeline implemented (Knowledge Base, Embeddings, Retrieval)
- [x] Knowledge base constructed using SQLite docs, HiveQL docs, and schema data
- [x] Text chunking and preprocessing pipeline implemented for RAG corpus
- [x] Embeddings generated using sentence-transformers (all-MiniLM-L6-v2)
- [x] FAISS vector store created for efficient similarity search
- [x] Retriever implemented with top-k semantic search
- [x] Cross-encoder reranker integrated (ms-marco-MiniLM-L-6-v2)
- [x] RAG-augmented dataset constructed with context injection
- [x] Baseline (No-RAG) and RAG datasets prepared for comparison
- [x] Experiment 1: Baseline model (T5 + LoRA) executed
- [x] Experiment 2: Learning rate tuning conducted
- [x] Experiment 3: Scheduler variants tested (Linear, Cosine, Constant)
- [x] Experiment 4: Beam search optimization performed
- [x] Experiment 5: Optimizer comparison (AdamW vs Adafactor)
- [x] Experiment 6: Regularization techniques applied (dropout, weight decay, label smoothing)
- [x] Experiment 7: RAG integration into training pipeline
- [x] Experiment 8: RAG ablation study (retriever vs reranker)
- [x] Experiment 9: Final optimized configuration executed
- [x] Validation performance evaluated using exact match metric
- [x] Experiment results logged and saved (milestone4_experiment_log.csv)
- [x] Key observations and insights documented
- [x] Milestone 4 Report finalized
- [x] Contributions document prepared
- [x] Presentation slides created for Milestone 4
- [x] Subtasks and deliverables submitted

### Milestone 5 (Completed)
- [x] Milestone 5 directory created
- [x] Best-performing model from Milestone 4 loaded (T5 + LoRA)
- [x] Evaluation pipelines implemented for multiple variants (Baseline, RAG, FSP, CoT, Full)
- [x] Batched inference pipeline implemented for scalable prediction generation
- [x] Beam search decoding applied (num_beams = 4)
- [x] Predictions generated across all pipeline variants
- [x] Full evaluation metric suite implemented: Exact Match (EM%), BLEU Score, Parse Validity (PV%), Token-Level F1 (TF1%), ROUGE-L (RL%)
- [x] Training logs analyzed for convergence and stability insights
- [x] Hyperparameter impact studied (weight decay, dropout)
- [x] Exact Match vs BLEU behavior analyzed
- [x] Parse validity issues identified and documented
- [x] Regularization effects (dropout) evaluated
- [x] Overfitting patterns analyzed using training vs validation loss
- [x] Pipeline comparison conducted (Baseline → RAG → FSP → CoT → Full)
- [x] Few-Shot Prompting (FSP) analysis performed: Token budget breakdown, Example relevance analysis, Complexity-wise contribution
- [x] Chain-of-Thought (CoT) analysis performed: Reasoning validation, Accuracy comparison, Impact assessment
- [x] Retrieval quality evaluation implemented using heuristic scoring
- [x] Per-complexity performance analysis conducted (Simple, Medium, Complex)
- [x] Comparative visualizations generated (pipeline vs metrics)
- [x] Key insights and observations documented
- [x] Final conclusions derived from evaluation results
- [x] Milestone 5 report finalized
- [x] Contributions document prepared
- [x] Presentation slides created for Milestone 5
- [x] Subtasks and deliverables submitted

### Milestone 6 (In Progress)
- [ ] Milestone 6 directory created
- [ ] System integration planning (end-to-end pipeline)
- [ ] Backend API design for SQL refactoring assistant
- [ ] Integration of model with inference pipeline
- [ ] Real-time query input and response handling
- [ ] UI/Dashboard design for user interaction
- [ ] Integration of RAG pipeline into production workflow
- [ ] Optimization of inference latency and performance
- [ ] Implementation of execution-based evaluation (optional)
- [ ] Testing and validation of complete system
- [ ] Documentation for deployment and usage
- [ ] Subtasks and deliverables in progress
