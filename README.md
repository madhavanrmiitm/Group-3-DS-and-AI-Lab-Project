# DSAI-Lab-T12026-Group-3: GenAI-Powered SQL Refactoring Assistant

## Project Overview

This project develops a **GenAI-powered SQL Refactoring Assistant** that automates the translation of SQL queries from SQLite to Apache Hive (HiveQL). The system leverages state-of-the-art techniques including Retrieval-Augmented Generation (RAG), few-shot prompting, and chain-of-thought reasoning to ensure syntactically correct and functionally equivalent SQL translations.

## Project Objectives

1. **Accept SQLite Queries**: Process valid SQL queries written in SQLite dialect
2. **Generate Equivalent HiveQL**: Produce functionally equivalent SQL queries in HiveQL that generate identical result sets
3. **Ensure Strict Syntax Compliance**: Utilize RAG to guarantee adherence to Hive SQL syntax specifications
4. **Scalability**: Design a one-to-many migration architecture that can be extended to other database pairs (e.g., MySQL → SparkSQL)

## Key Problem Areas Addressed

- **Type System Migration**: Translating from SQLite's dynamic typing to Hive's static typing
- **Date/Time Function Conversion**: Rewriting SQLite's `strftime` functions to Hive's `from_unixtime` equivalents
- **Quote Handling**: Correcting quote differences between systems (single vs. double ticks)
- **Distributed Execution Model**: Adapting queries for Hive's distributed execution environment

## Technical Approach

### Methodology
- **Mix of RAG and Prompting**: Combines Retrieval-Augmented Generation, few-shot examples, and chain-of-thought reasoning
- **LLM Support**: Evaluates performance across ChatGPT, Claude, Gemini, and DeepSeek
- **Comprehensive Validation**: Two-layer evaluation framework combining execution testing and CodeBLEU metrics

### Evaluation Strategy

**Layer 1: Execution Accuracy (Gold Standard)**
- Score 1 – Compilation Success: Verify translated queries run without errors in Hive
- Score 2 – Data Equivalence: Compare result sets between original SQLite and translated Hive queries

**Layer 2: Syntax and Structural Similarity**
- CodeBLEU metric to evaluate code quality beyond exact text matching
- Abstract Syntax Tree (AST) comparison for structural analysis

### Dataset
- **Primary Dataset**: PARROT benchmark for cross-system SQL translation (from Hugging Face: `weizhoudb/PARROT`)
- Filtered to extract SQLite-to-Hive query pairs for standardized ground truth

## Repository Structure

To be updated. Project in progress.
