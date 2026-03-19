# Milestone 3 Report

### 1. Overview
For Milestone 3, our primary goal was to design, justify, and verify the model architecture that will power our SQL refactoring assistant. Based on our experimental code, we have successfully implemented a complete end-to-end pipeline using a Text-to-Text Transfer Transformer architecture. We ran a subset of our data through this full pipeline to confirm that data preprocessing, tokenization, model generation, and output decoding all are working properly. 

---

### 2. Dataset Organization and Directory Structure
To keep our workflow modular and reproducible, we are planning to organize our dataset and codebase into a clean directory structure.

* `/data/raw/`: Initial PARROT benchmark dataset loaded via the HuggingFace datasets library will be kept here.
* `/data/processed/`: Cleaned and validated SQLite-to-Hive pairs. As shown in our data preparation script, we dropped missing values, removed duplicates, and parsed the SQL using the 'sqlglot' library to remove any structurally malformed queries. This data after these steps will be stored here.
* `/data/splits/`: It will contain the final datasets ready for model ingestion. As demonstrated in our code, we utilized scikit-learn's 'train_test_split' to partition the data. For the pipeline verification, we took a clean subset of 668 pairs and split them into Train (466), Validation (100), and Test (101).

---

### 3. Detailed Preprocessing and Tokenization Steps
Before any data touches the model, it goes through a strict preprocessing and tokenization phase. We implemented the following steps in our code to ensure the model receives optimal inputs:

* **Syntax Validation and Normalization:** We used 'sqlglot.parse_one()' to verify that every query is syntactically valid. We then applied a custom 'normalize_sql()' function to convert all text to lowercase and strip excess whitespace. We used this specific component because feeding inconsistently formatted or broken SQL into a generative model drastically degrades its learning efficiency.
* **Text Formatting:** For every input, we prefixed the string: "translate sqlite to hive: ". This specific component was used because our chosen architecture (T5) relies on task-specific text prefixes to condition the encoder on what exactly it needs to do.
* **Tokenization:** We utilized the 'T5Tokenizer'. We set our 'MAX_LEN' to 128 tokens for this pipeline verification. We applied 'padding="max_length"' and 'truncation=True'. This guarantees that every query, regardless of its original character length, is converted into a uniform numerical format that the neural network can process as a batch.

---

### 4. Architecture Description and Component Interaction
For our basic architecture, we used the T5 (Text-to-Text Transfer Transformer) model, especially 'T5ForConditionalGeneration' ("t5-small"). 

T5 is an Encoder-Decoder transformer. The primary elements connect as follows: 
* Bidirectional processing of our task prefix-bearing tokenized SQLite query occurs via the Encoder. Hidden states let one construct a rich, context-based mathematical model of the whole input SQL sequence. 
* Token by token, the Decoder autoregressively creates the target HiveQL query from the encoder's buried states. To make sure the produced HiveQL fits the input SQLite logic structurally, cross-attention enables it to attend to both previously produced tokens and the encoder's output.

---

### 5. Data Flow Diagram
Here is the step-by-step data flow from raw input to the final model output:

```text
[Raw SQLite Query]
        |
        |
        V
[Text Normalization & Task Prefixing] ---> Translate sqlite to hive
        |
        |
        V
[T5 Tokenizer] ---> Converts text to Input IDs & Attention Masks
        |
        |
        V
[T5 Encoder] ---> Processes the sequence bidirectionally into Hidden States
        |
        |
        V
[T5 Decoder] ---> Generates Target IDs (Logits) step-by-step
        |
        |
        V
[Tokenizer Decode] ---> Converts Target IDs back to human-readable text
        |
        |
        V
[Post-Processing] ---> Strips padding/special tokens
        |
        |
        V
[Final HiveQL Output]
```

### 6. Input Format, Tensor Shapes, and Token Lengths
The rigorous input demands of the T5 model are precisely met by our treated data.

* Given that we employed 'return_tensors="pt"' along with 'padding="max_length"' and a 'MAX_LEN' of 128, every single input batch submitted to the model has a strict tensor shape of `[Batch_Size, 128]`. 
* The PyTorch tensors ('torch.Tensor') given straight to the device (GPU/CUDA if available, otherwise CPU) are tensor formats. 
* For the forward pass, the model anticipates two main inputs: 'input_ids' (the actual tokenized numerical representations) and the 'attention_mask' (a binary tensor of 1s and 0s instructing the model to disregard the padding tokens).

---

### 7. Suitability of the Chosen Architecture

We selected the T5 (Text-to-Text Transfer Transformer) architecture because it models all tasks as text-to-text transformations, which directly aligns with our goal of translating SQLite queries to HiveQL.

T5’s encoder-decoder structure is well-suited for this task. The encoder captures the full context of the input query bidirectionally, while the decoder generates the target query step-by-step using cross-attention. This ensures strong structural alignment between input and output, which is significant for SQL translation.

In comparison with decoder-only models, T5 enables better control for structured transformations. Although we used the smaller t5-small variant, which has limited context and domain knowledge, it remains efficient and reliable for our parallel dataset setting, with lower risk of insignificant or hallucinated outputs.

---

### 8. Small-Scale Pipeline Verification

We validated the pipeline using 10 samples from the test dataset `(test_df.head(10))`.

Each sample passed through the complete workflow:
* Task prefixing and preprocessing
* Tokenization with max_length = 128
* Tensor conversion and device transfer
* Inference using `model.generate()`
* Decoding and cleaning of outputs

The pipeline executed successfully without errors, confirming that all components—tokenization, model inference, and decoding—are accurately integrated and stable.

---

### 9. Model Outputs, Loss Function, and Evaluation Metrics

**Model Outputs:** 
The pretrained (non-fine-tuned) model generated SQL-like outputs that preserved key structural elements such as SELECT, FROM, and aggregations, though exact HiveQL syntax was not fully matched.

**Loss Function:**
Training uses Cross-Entropy Loss, computed internally by T5:
* Compares predicted tokens with ground truth
* Uses teacher forcing
* Ignores padding tokens

**Evaluation Metrics:** 
We used `difflib.SequenceMatcher` to measure similarity between predicted and actual queries.

**Result:** 
Average similarity ≈ 0.78 (78%), indicating strong structural alignment even before fine-tuning. Future evaluation will include CodeBLEU and Execution Accuracy for deeper validation.

---

### Presented By Group 3

**AJAY (21f1005414) \
MADHAVAN R MOHAN (22f3000983) \
SANJAY RAJESH MANWANI (21f3002914) \
SENTHILKUMAR N (21f1006434) \
VERAL SHARMA (22f1001101)**
