# Predicting Compilation and Memory Failures from LLM Explanations

**Status:** Double-Blind Peer Review (XKDD 2026)

This repository contains the complete replication package, including the multi-step text generation, extraction, dynamic evaluation scripts, and the full 8,551-pair dataset for our study investigating whether natural-language pedagogical artifacts produced by Large Language Models (LLMs) can diagnose software quality.

Our audit evaluates three hosted frontier models — **GPT-OSS 120B**, **Llama-3.3 70B**, and **Moonshot Kimi-K2 0905** — across 2868 LeetCode-derived algorithmic C challenges using static code compilation and dynamic runtime instrumentation.

---

## Dataset Overview
The finalized dataset contains **8551 validated model–problem records**. For every test case execution, the replication package tracks and provides:
* **Pedagogical Registers:** Raw step-by-step technical explanations, Socratic scaffolded hint ladders, and learning objectives summaries.
* **NLP Structural Encodings:** Lowercased and stemmed 300-max-feature TF-IDF sparse matrices, 1152-dimensional dense contextual Sentence-BERT (`all-MiniLM-L6-v2`) embeddings, and 12 scalar structural proxies (including Flesch-Kincaid grade level, Gunning Fog indexes, type-token lexical diversity, and summary–code operational keyword alignment scores).
* **Dynamic Target Labels:** Strict binary compilation results (`Target_Compilation`) under GCC and dynamic execution outcomes (`Target_Memory_UB`) monitored using Valgrind and UndefinedBehaviorSanitizer (UBSan).

---

## Replication Guide

This repository provides step-by-step Jupyter notebooks for a complete reproduction of our pipeline.

### 1. OpenRouter Multi-Turn Prompting Pipeline
Run `XKDD_01_Phase2_External_Audit.ipynb` to execute the automated data collection loop via OpenRouter. 
* Ingests the filtered problem bank rows and triggers a stateful, multi-turn dialogue with the target endpoints to sequentially output the program, explanation prose, hint matrices, short recaps, and testing specifications while isolating and filtering API rate limits.

### 2. Automated Technical Sandbox Evaluation
Run `XKDD_02_External_Technical_Evaluation.ipynb` to clean the generated string fields and route them through local sandbox compilation and dynamic runtime verification utilities.
* **Extraction:** Trims latent reasoning metadata tags and extracts raw C code and machine-readable JSON testing manifests.
* **Static Assessment:** Drives solutions through `gcc` (v11.4.0) with optimization and rigorous diagnostic parameters (`-O2 -Wall -std=c11`) to label compilation state.
* **Dynamic Analysis:** Builds parallel instrumentation binaries with runtime safety trainers (`-fsanitize=undefined`) and feeds standard testing streams directly into `valgrind` (v3.18.1) to flag dynamic allocation vulnerabilities, definite leak conditions, invalid reads/writes, or infinite loops. Matches outputs against the target matrix to form a cohesive evaluation dataset.

### 3. NLP Feature Extraction & Machine Learning Engine
Run `XKDD_Phase_03_Evaluation.ipynb` to read the aggregated evaluation logs and construct the text-only prediction backbones.
* **Featurization:** Runs NLTK tokenization, stop-word purges, Porter stemming arrays, caps sparse vocabularies, and loads HuggingFace embedding utilities. Computes scalar style metrics with `textstat` and calculates token-level overlap.
* **Modeling:** Sets up stratified 10-fold cross-validation and Leave-One-Model-Out (LOMO) parameters to train Random Forest and Logistic Regression classifiers.
* **Interpretability:** Outputs global discrimination metrics, comparative bar-chart ablations across each linguistic register, and maps exact `SHAP` beeswarm plots using a TreeExplainer array to visualize keyword risk distributions.

---

## Environment Requirements
To reproduce the engineering sandbox and modeling jobs locally, ensure your Linux workspace (or WSL2 layer) has the following dependencies initialized:
* **System Utilities:** `gcc` (>= 11.4.0), `valgrind` (>= 3.18.1)
* **Python Environments:** Python 3.10+ containing `scikit-learn`, `sentence-transformers`, `textstat`, `shap`, `nltk`, `pandas`, `numpy`, `matplotlib`, `seaborn`, and `tqdm`.

---

## License
This suite and the accompanying text corpus are licensed under an anonymized MIT License for double-blind workshop verification.
