# Milestone 2 Report


## 1. Overview

Our project aims to build a Retrieval-Augmented Generation (RAG) based LLM system that translates SQL queries from the SQLite to Apache HiveQL while being functionally equivalent. This report includes identification, quality assessment, preprocessing, and structuring of our three primary data sources: the parallel query corpus, the schema contexts, and the official documentation for the RAG based generation.

---

## 2. Data Sources Identification & Verification

We relied on three distinct data sources to ensure structural accuracy, schema awareness, and syntax compliance.

### 2.1 Primary Dataset:

The primary dataset is a publicly available parallel SQL corpus. It is designed for cross-system SQL translation.

- **Dataset Name:** PARROT  
- **Source:** Hugging Face (weizhoudb/PARROT).  
- It is available in Structured JSON/CSV format.  
- This dataset is publically available and it has no commercial restrictions. We are filtering the full dataset exclusively for SQLite → HiveQL translation pairs and using it for our project.

### 2.2 RAG based approach

To ground the LLM and prevent the hallucination of unsupported functions we utilize official documentation. We are using Apache Hive Language Manual and SQLite Core Language Manual for RAG based generation.

Reference : [SQLite Docs](sqlite.org/lang.html) and [Apache Hive Docs](hive.apache.org/docs)

We have used these documentation for DDL/DML syntax rules, built-in function mappings and type system references.

### 2.3 Data Models & Schemas

Translating SQL correctly requires context especially for dynamic to static typing.

Source: Extracted from the PARROT dataset environments and augmented with standard normalized database schemas

Mainly we are using table definitions, column data types and primary/foreign key relationships corresponding to the queries in the PARROT dataset.

By using these sources, we are trying to decrease hallucinations and generate functionality equivalent SQL queries in HiveQL from SQLite.

## 3. Dataset Description & Feature Distribution

After filtering the PARROT dataset to retain only the SQLite to HiveQL translation pairs, the resulting dataset contains a curated collection of parallel SQL queries used for training the translation model.

### 3.1 Dataset Size and Composition

The dataset prepared for this project consists of the following components:

- **Estimated Dataset Size:** Approximately 1,000 to 2,000 high-quality parallel query pairs where each SQLite query has a corresponding HiveQL translation.

- **Schema Contexts:** Around 50 to 100 unique database schemas are associated with these queries. These schemas provide the structural context required for understanding table relationships and column usage.

- **RAG Knowledge Corpus:** To support the Retrieval-Augmented Generation framework, a corpus of 2,000 to 5,000 document chunks has been extracted from official database manuals and documentation.

- These chunks provide reference material that helps the model understand syntax differences between SQLite and HiveQL.

### 3.2 SQL Feature Distribution

To ensure that the model learns to handle different levels of SQL translation complexity, the dataset includes queries covering a broad spectrum of SQL features. These features are grouped into three levels:

#### Low Complexity Queries

These queries involve basic SQL operations and syntax differences between the two dialects.

Examples include:

- Basic SELECT statements  
- WHERE clauses  
- LIMIT clause differences used for pagination

#### Medium Complexity Queries

These queries introduce more advanced SQL constructs that require structural translation.

Examples include:

- JOIN operations  
- String manipulation functions, such as  
- SUBSTR (SQLite) → SUBSTRING (HiveQL)  
- Common Table Expressions (CTEs)

#### High Complexity Queries

These queries require deeper semantic understanding and dialect-specific function mapping.

Examples include:

- Date and time function conversions, such as  
- strftime → from_unixtime  
- Complex Window Functions  
- Dynamic-to-static type casting differences

This distribution ensures that the dataset includes both simple and advanced SQL constructs, enabling the model to learn accurate dialect translation across varying levels of complexity.

---

## 4. Dataset Quality Assessment

The PARROT dataset used in this project is a peer-reviewed benchmark designed for cross-system SQL translation. Since it is created for research purposes, the dataset already undergoes initial validation by its creators. However, additional quality checks are performed during preprocessing to ensure the dataset is clean and reliable for training.

These checks focus on detecting missing values, duplicate entries, malformed SQL queries, encoding inconsistencies, and schema mismatches, ensuring that only valid and meaningful query pairs remain in the dataset.


### 4.1 Handling Missing Values

Each record contains a SQLite source query and its corresponding HiveQL target query. During preprocessing, the dataset is scanned for null or empty fields in either query. Any incomplete query pairs are flagged and removed, ensuring that all remaining records contain valid source–target pairs.

### 4.2 Duplicate Entry Detection

To avoid repeated patterns in training, the dataset is checked for exact duplicate query pairs. A hash-based deduplication method is applied to the pair (source_query, target_query). Identical pairs are removed, while structurally different or paraphrased queries are retained since they help the model learn variations in SQL expressions.

### 4.3 Noise and Malformed SQL Detection

Both SQLite and HiveQL queries are validated using the sqlglot Python library, which attempts to parse each query into an Abstract Syntax Tree (AST). Queries that fail to parse are considered malformed or noisy and are flagged and quarantined to prevent invalid SQL from entering the training data.

### 4.4 Label and Encoding Consistency

The dataset is also checked for dialect label mismatches and encoding issues. Queries are verified to ensure they follow the expected syntax of SQLite and HiveQL.

Additionally, text is normalized to UTF-8 encoding, and quotation styles (single quotes, double quotes, backticks) are standardized for consistency.

### 4.5 Schema–Query Validation

Finally, queries are validated against their associated database schemas to confirm that all referenced tables and columns actually exist. Queries with schema mismatches are flagged and excluded to ensure accurate training and evaluation.

---

## 5. Dataset Adequacy & Augmentation Strategy

### 5.1 Dataset Adequacy

The PARROT SQLite → Hive subset provides a strong foundation in terms of data quality, as it was specifically designed for SQL dialect translation while maintaining functional equivalence between queries. However, the dataset size (~1,500 query pairs) sits near the lower bound typically recommended for reliable LLM fine-tuning.

While sufficient for experimentation, expanding the dataset can improve model robustness and generalization.

### 5.2 Augmentation Strategy (Synthetic Data Generation)

To increase dataset volume without introducing external noise or inconsistencies, we will generate additional training examples using the existing data models and schemas.

The augmentation process consists of three stages:

#### Synthetic Query Generation

New SQLite queries will be created programmatically using rule-based combinatorial transformations applied to existing PARROT queries. Examples include modifying column selections, altering JOIN conditions, adjusting WHERE filters, or substituting schema elements while preserving query validity.

#### Deterministic Translation

These synthetic SQLite queries will then be translated into HiveQL using rule-based transpilation implemented through sqlglot.

#### Execution Validation

Both the original SQLite query and the generated HiveQL query will be executed in a controlled Docker environment. Only pairs that produce functionally equivalent results will be retained and added to the training dataset.

This pipeline allows us to expand the dataset while maintaining the same quality and semantic correctness guarantees as the original PARROT data.

---

## 6. Dataset Split & Leakage Prevention

The dataset will be divided into **60% Training, 20% Validation, and 20% Test sets.**

### 6.1 Stratified Splitting

To ensure balanced evaluation, the split is stratified according to query complexity, measured using AST (Abstract Syntax Tree) depth scoring. This ensures that each subset contains a representative mix of simple, moderate, and complex SQL queries.

### 6.2 Data Leakage Prevention

Several safeguards are implemented to prevent information leakage between splits:

#### Exact Deduplication

Hash-based checks ensure that identical query pairs do not appear in multiple dataset splits.

#### Paraphrastic Similarity Detection

To identify near-duplicate queries, we apply MinHash and Jaccard similarity screening with a similarity threshold of 0.85. Queries that exceed this threshold are grouped and assigned to the same split.

#### Schema-Level Isolation

When multiple queries share the same database schema and exhibit structural similarity, they are placed within the same split (typically the training set). This prevents the model from indirectly memorizing schema patterns that might otherwise appear in the test set.

Together, these measures ensure that model evaluation reflects true generalization performance rather than memorization of training patterns.

---

## 7. LLM Formatting and Prompt Structuring

- To fine-tune the Large Language Model (LLM), an Instruction–Response JSON structure is used to format the data. This approach ensures that the model learns the mapping between SQLite queries and their equivalent Apache HiveQL translations in a consistent and structured way.

- Each example consists of an instruction, which includes the task description, schema context, and the SQLite query, and a response, which details the corresponding HiveQL translation. Including the database schema in the instruction is a significant design choice, as SQL queries depend on table structures, column names, and data types. Providing this context helps the model generate more accurate translations and minimises ambiguity.

- To optimize performance and ensure compatibility with LLM training constraints, we utilize a standardized prompt template and enforce a maximum sequence length of 2048 tokens.

### Example dataset entry

```json
{
 "instruction": "Translate the following SQLite query to Apache HiveQL.

Schema:
CREATE TABLE orders (id INT, order_date TEXT, total REAL);

SQLite Query:
SELECT strftime('%Y-%m', order_date), sum(total) FROM orders GROUP BY 1;",
 
 "response": "SELECT from_unixtime(unix_timestamp(order_date, 'yyyy-MM-dd'), 'yyyy-MM'), sum(total) FROM orders GROUP BY from_unixtime(unix_timestamp(order_date, 'yyyy-MM-dd'), 'yyyy-MM');"
}
```

This formatting provides clear input-output pairs that allow the model to learn accurate SQL dialect translation.

---

## 8. RAG Document Preparation

- To enhance translation accuracy and minimize the generation of unsupported SQL functions, the system incorporates a Retrieval-Augmented Generation (RAG) component. The knowledge base is derived from official SQLite and Apache Hive documentation, which provides reliable information about SQL syntax, functions, and dialect-specific rules.

- To manage the documentation effectively, a strategic chunking process is implemented. Each document is divided into **512-token chunks with a 64-token overlap** to maintain contextual continuity. A semantic boundary splitter is also utilised to ensure that logical sections such as function definitions remain intact.

- Each chunk is enriched with metadata tags such as dialect (Hive or SQLite), content type (e.g., date functions or syntax rules), and source. Through this metadata relevant information is more efficiently identified by the retriever.

- The text chunks are transformed into vector embeddings using a model like **text-embedding-3-small**. For storage and retrieval, **ChromaDB** is used during the development phase for its robust feature set, transitioning to **FAISS** for a lightweight production deployment.

- During the inference stage, the system fetches the most pertinent documentation chunks to serve as in-context guidance for the LLM. This ensures that the resulting HiveQL queries strictly adhere to official function mappings and standardized syntax rules.

---

## 9. Preprocessing Pipeline & Reproducibility

To guarantee full reproducibility and transparency in the dataset preparation process, the preprocessing pipeline is designed with the following principles:

### Deterministic Operations

All stochastic processes are controlled using a fixed random seed (**seed = 42**). This ensures consistent behavior for operations such as data shuffling, dataset splitting, and MinHash-based deduplication across repeated runs.

### Controlled Environment

The entire data processing workflow is implemented in **Python 3.11** with strictly pinned dependency versions (e.g., datasets==2.x, sqlglot==23.x, scikit-learn==1.x). These dependencies are documented in a **requirements.txt** file to ensure the environment can be reliably recreated.

### Traceability & Auditability

The preprocessing pipeline is structured as a sequence of modular stages:

```
Load → Filter → Deduplicate → Validate → Schema Injection → Split
```

Each stage produces timestamped intermediate artifacts stored as JSON files. Additionally, a detailed run log records metrics such as the number of queries filtered, flagged, or removed at each step, enabling straightforward auditing and debugging of the pipeline.

---

## Appendix

### Milestone 1 Feedback from TA
TA told us to add the information about stakeholders.
- **Stakeholders:** Industry/Orgnaization working in data migration

---

### Presented By Group 3

**AJAY (21f1005414) \
MADHAVAN R MOHAN (22f3000983) \
SANJAY RAJESH MANWANI (21f3002914) \
SENTHILKUMAR N (21f1006434) \
VERAL SHARMA (22f1001101)**
