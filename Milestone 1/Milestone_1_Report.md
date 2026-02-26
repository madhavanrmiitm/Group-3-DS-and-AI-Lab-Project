# Milestone 1 Report

## 1. Problem Definition and Project Objectives

### 1.1. Background and Context
Business organizations are increasingly migrating their data infrastructure from traditional, on-premise, lightweight databases (SQLite, MySQL etc.) to cloud-native, distributed big data warehouses (Apache Hive,Spark etc). This transition is essential for scaling operations. Cloud providers provide various advantages over the traditional databases like scalability, cost effectiveness, improved security and efficient environment for collaboration or remote work. Alongside these advantages shifting to these cloud based databases brings a significant technical challenge of database migration. Migration requires refactoring thousands of legacy SQL queries to function correctly in the new environment.

### 1.2. The Problem
These database platforms differ significantly from one another. The differences range from syntax to semantics that includes data handling, string manipulation, and window functions etc. Currently, manual refactoring is the standard approach, but it is highly error-prone, time-consuming, and strictly requires dual expertise in both the source and target SQL dialects.

### 1.3. Objectives
The primary objective of this project is to develop a GenAI-powered SQL Refactoring Assistant that automates the translation of SQL queries.
- The system will accept a valid SQL query written in the source dialect (SQLite).
- The system will generate a functionally equivalent SQL query written in the target big-data dialect (HiveQL). This new query will generate the exact same result set when executed. That is the goal of this project.
- **To ensure strict syntax compliance,** the system will utilize Retrieval-Augmented Generation (RAG).

### 1.4. Scope and Justification
We have selected SQLite to Apache Hive migration pairs for our project. This pair has a high level of complexity and widely used in the industries.
- This pair represents a classic "Scale-up" scenario where code written for local prototyping (SQLite) must be refactored for a production ready distributed database (Hive).
- This translation will handle different structural differences between the two dialects, as stated below
  - Typing from SQLite's dynamically typed system to Hive's statically typed system.
  - Bridging significant differences in Date/Time functions. For example, rewriting queries that use SQLite's strftime to use Hive's from_unixtime
  - Correcting quote handling (double vs. single ticks)
While the immediate scope focuses on the SQLite-to-Hive pipeline. There is also a more generic objective, which is Scalability. The architecture will be designed in such a way that, utilizing a One-to-Many migration approach, the model can theoretically be fine-tuned for other migration pairs (e.g., MySQL → SparkSQL) in the future.

---

## 2. Literature Review and Existing Solutions

### 2.1 Papers Referred

**SQL-GEN:** https://arxiv.org/html/2408.12733v2  
NL2SQL works best for the system to be built, not for those systems which are planned for migration to advanced Big data/Cloud platforms as it requires rewriting the existing schema and tables.

**CrackSQL:** https://arxiv.org/pdf/2504.00882  
This paper combines rule based and general purpose LLM (prompt-based) techniques to handle the cross SQL translation for Oracle to MySQL. Here LLM based translation is limited to queries which require rewriting. Lack of RAG based systems generates hallucinated responses.

**MALLET:** https://dspace.mit.edu/handle/1721.1/155537  
This paper proposes a LLM based translation using RAG fed with relevant documentation. This paper focuses on schema and basic functionality translation. We are leveraging this approach and work on improving on the existing architecture to handle chain of thought prompting apart from few shot examples used in this approach

**RISE:** https://arxiv.org/html/2601.05579v1  
This paper is an improvement over MALLET which implements chain of thought prompting but lacks RAG based system.

**PARROT:** https://arxiv.org/abs/2509.23338  
This paper helps us to evaluate the cross-system SQL translation in terms of  **Relevance**, **Scalability**, **Simplicity** and **Portability**. We'll be working on benchmarking our approach along these 4 categories.

### 2.2 Suggested Approach
- **Mix of RAG and Prompting** - Few shots and chain of thought  
   - **RAG** - Relevance  
   - **Few shots** - Accuracy  
   - **Chain of Thought** - Reasoning  
- To train our model across ChatGPT, Claude, Gemini, DeepSeek and evaluate the performance.

---

## 3. Baselines and Evaluation Benchmarks

### 3.1 Primary Dataset and Literature Baseline
To build a strong and reliable baseline for our SQLite-to-Hive SQL refactoring system, we will use the PARROT dataset, which is publicly available on Hugging Face under weizhoudb/PARROT. PARROT is a benchmark specifically created for cross-system SQL translation. It contains parallel SQL query pairs across multiple database dialects. For this project, we will filter the dataset to extract only the relevant (SQLite, Hive) query pairs. These pairs provide a standardized ground truth for evaluating how well a model translates SQL queries from SQLite syntax to HiveQL syntax.

The reference paper associated with this dataset, "PARROT: A Benchmark for Evaluating LLMs in Cross-System SQL Translation" (arXiv: 2509.23338), reports benchmark accuracy scores for different state-of-the-art language models on various dialect translation tasks, including SQLite-to-Hive. We will use the reported SQLite-to-Hive translation accuracy from this paper as our primary baseline. The performance of our fine-tuned model and our Retrieval-Augmented Generation (RAG) enhanced model will be compared directly against these published results. This comparison will allow us to clearly measure and quantify any improvement achieved by our approach.

### 3.2 Proposed Evaluation Strategy
Our goal is not only to generate syntactically valid SQL but to ensure minimal friction and functional correctness. Simply comparing generated queries using exact text matching is not sufficient, because two SQL queries may look different but still produce the same result.  
To address this, we propose a two-layer evaluation framework.  
- **Layer 1: Execution Accuracy (The Gold Standard)**  
  The most important measure of correctness in SQL translation is whether the translated query runs successfully and produces the correct output. To evaluate this, we will set up a local testing environment using Docker containers for both SQLite and Apache Hive. Each translated Hive query generated by our model will be tested using two checks:  
  - **Score 1 – Compilation Success**  
    We will verify whether the translated Hive query runs in the Hive environment without any syntax or runtime errors.  
  - **Score 2 – Data Equivalence**  
    We will compare the result set produced by the translated Hive query with the result set produced by the original SQLite query. If both outputs match exactly, the translation will be considered functionally correct.  
- **Layer 2: Syntax and Structural Similarity (CodeBLEU)**  
  Since strict text matching does not fully capture the quality of code generation, we will also use a structural similarity metric such as CodeBLEU. Unlike standard BLEU, which measures only n-gram overlap, CodeBLEU evaluates code by considering syntactic and semantic features. It compares elements such as the Abstract Syntax Tree (AST) of the generated query with that of the reference Hive query. This helps assess how closely the generated SQL follows proper HiveQL structure, even if minor textual differences exist.  
Using this two-layer approach allows us to evaluate both functional correctness (through execution testing) and structural quality (through semantic similarity analysis).

---

## 4. Gaps and Research Opportunities

While some impressive strides have been observed in Text-to-SQL and SQL translation research, there are still a few significant gaps, especially pertaining to migrating SQL queries from traditional databases like SQLite to distributed cloud platforms such as Apache Hive.

### 4.1 Limited Focus on Cross-Dialect Refactoring
A lot of the current research focuses on generating SQL from natural language, but a key area of translating SQL queries between different dialects has not been focused upon. Datasets like PARROT do offer parallel SQL corpora, yet many methods mainly aim for textual similarity or execution accuracy in controlled benchmarks. Unfortunately, these approaches seem ineffective when it comes to addressing deeper semantic mismatches, function-level transformations (like how dates are handled), or typing inconsistencies between the source and target systems. Additionally, SQLite and Hive have some important and noticeable differences in their typing systems, execution models, and built-in functions. However, much of the existing literature tends to treat these dialect differences as just minor syntactic modifications, ignoring the architectural differences like distributed execution requirements and strict type enforcement.

**Research Opportunity:**  
The need of the hour is a semantic-aware refactoring system that can grasp the intent behind posed queries and the structural differences between dialects, rather than just restricting to skimming the surface with syntactic translation. Through the use of schema-aware Retrieval-Augmented Generation (RAG), we can enable the dynamic retrieval of metadata, function documentation, and type constraints, ensuring that the generated queries are in sync with the target dialect.

### 4.2 Limitations in LLM-Based Code Generation
Large Language Models (LLMs) can stimulate the surfacing of queries that look good but might not actually work or make sense when explored in depth. A lot of the current methods focus on fine-tuning but often skip the step of grounding their outputs against reliable documentation. Most existing studies measure performance using metrics like Exact Match or BLEU. However, when SQL migration tasks are focused upon, correct execution and matching up with the result holds great significance.

**Research Opportunity:**  
Integrating Retrieval-Augmented Generation (RAG) with rule-based validation and execution testing can cut down on these hallucinations and enhance the reliability and accuracy of the SQL queries that are generated. Furthermore, a dual-layer evaluation approach—integrating execution-based checks and CodeBLEU metrics—ensures a more detailed and practical perspective on the system's actual performance capabilities.

---

### Presented By Group 3

**AJAY (21f1005414) \
MADHAVAN R MOHAN (22f3000983) \
SANJAY RAJESH MANWANI (21f3002914) \
SENTHILKUMAR N (21f1006434) \
VERAL SHARMA (22f1001101)**
