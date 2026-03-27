# Cost-Latency Tradeoff in LLM-Based Systems  
### Prompting vs RAG vs Context Optimization (API vs Local Models)

This repository contains the full implementation, datasets, and experimental pipeline for analyzing **cost, latency, and accuracy trade-offs** in Large Language Model (LLM) systems.

The project compares three architectures:

- **Prompt-Only**
- **Retrieval-Augmented Generation (RAG)**
- **Context-Optimized Systems**

across two deployment settings:

- **API-based model**: Gemini 2.5 Flash (via Google Vertex AI)  
- **Local open-weight model**: Qwen 2.5 1.5B Instruct  

---

## Key Objective

Modern LLM systems are not just about accuracy — **cost, latency, and hardware feasibility** are critical in real-world deployments.

This project answers:

> *Which LLM architecture provides the best trade-off between accuracy, cost, and latency under different deployment constraints?*

---

## Key Findings

- **Prompt-Only**
  - Infeasible for local models (0% accuracy, very high latency)
  - Extremely costly for API usage (~79k tokens/query)

- **RAG (Retrieval-Augmented Generation)**
  - Lowest latency (~3s) and token usage
  - Best for **resource-constrained local environments**
  - Slight drop in exact-match accuracy

- **Context-Optimized System**
  - Best **cost–accuracy balance** for API models
  - Matches prompt-only accuracy with ~40% fewer tokens
  - Preserves document coherence better than RAG

> Note: API cost measurements are based on token usage via Vertex AI pricing. Actual cost may vary depending on region and billing configuration.

---

## System Architectures

### 1. Prompt-Only
- Entire document passed directly into the model
- No preprocessing or retrieval
- High token usage and cost

### 2. RAG Pipeline
- Document chunking (256 tokens + overlap)
- Embedding using `all-MiniLM-L6-v2`
- FAISS vector search (Top-k retrieval)
- Retrieved chunks passed to LLM

### 3. Context-Optimized
- Global chunk ranking using similarity
- Selects best chunks within a token budget
- Maintains better context coherence than RAG

---

## 📚 Dataset

- 5 EU Regulatory Documents:
  - GDPR
  - AI Act
  - Digital Services Act (DSA)
  - Digital Markets Act (DMA)
  - Data Governance Act (DGA)

- 100 manually created QA pairs
  - Extractive answers (exact spans)
  - Covers definitions, obligations, thresholds, and cross-references

---

## Evaluation Metrics

- **Exact Match (EM)** – Answer correctness  
- **Latency (seconds)** – End-to-end runtime  
- **Token Usage** – Proxy for cost  

---

## Tech Stack

- Python
- Hugging Face Transformers
- FAISS (vector search)
- Sentence Transformers
- PyTorch
- Google Vertex AI
- Google Cloud SDK (`gcloud`)

---

## API Access (Vertex AI)

The Gemini 2.5 Flash model is accessed via **Google Vertex AI**, which provides managed, scalable infrastructure for LLM inference.

### Requirements

- Google Cloud account
- Vertex AI API enabled
- Service account with appropriate permissions

### Setup

1. Install Google Cloud SDK:
```bash
curl https://sdk.cloud.google.com | bash
gcloud init
