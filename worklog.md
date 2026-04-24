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

### Milestone 6 (Completed)
- [x] Milestone 6 directory created
- [x] Final best configuration selected (T5-base full fine-tuning + CodeBERT + FAISS RAG)
- [x] End-to-end pipeline integrated (SQLite input → Retrieval → T5 generation → HiveQL output)
- [x] Inference pipeline implemented with RAG toggle and top-k retrieval
- [x] CodeBERT embedding pipeline integrated for query encoding
- [x] FAISS index built and loaded for real-time retrieval
- [x] Few-shot prompt construction using retrieved examples implemented
- [x] Core translation module finalized (src/translate.py)
- [x] Gradio UI implemented for interactive query translation
- [x] User controls added (RAG enable/disable, top-k selection)
- [x] FastAPI backend implemented with endpoints (/translate, /health)
- [x] API integration tested using curl and local requests
- [x] Deployment setup prepared for Hugging Face Spaces (Gradio app)
- [x] Colab demo notebook created as fallback deployment option
- [x] Project folder structure finalized for deployment readiness
- [x] Model checkpoint (FullPipeline-BestConfig) integrated for inference
- [x] Precomputed FAISS index bundled with deployment assets
- [x] Latency and performance tested (1–3 sec inference on T4 GPU)
- [x] Cold start behavior analyzed (~25 sec on HF Spaces)
- [x] Error handling implemented (invalid SQL, empty input, timeout handling)
- [x] SQL validation using sqlglot integrated for parse correctness
- [x] Logging and monitoring mechanisms added (latency, errors)
- [x] Deployment README created with setup and usage instructions
- [x] Comprehensive Technical Documentation prepared (architecture, pipeline, training, evaluation, API)
- [x] Final Project Report completed (end-to-end system description and results)
- [x] API documentation written (request/response format, endpoints)
- [x] User documentation created (usage steps, examples, troubleshooting)
- [x] Limitations and future work documented
- [x] Milestone 6 contributions document prepared
- [x] Presentation slides created for Milestone 6
- [x] Subtasks and deliverables submitted
