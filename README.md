# Spring 26 CSE Workshop: LLMs from Fundamentals to Building Real Apps (Hands On)

**Date:** March 2, 2026
**Duration:** 2 hours
**Format:** Hands-on workshop (code-first, live demos)
**Speakers:**

* **Part 1 (LLM Fundamentals / Transformers):** Dev Desai
* **Part 2 (LLM Engineering Stack):** Vansh Thakkar

---

## What you will learn

This workshop is designed to take you from **how LLMs work** to **how LLM products are built**.

### Part 1: LLM Fundamentals (Transformers)

Led by **Dev Desai**

* Why Transformers replaced RNNs and classical NLP
* Attention intuition and how self-attention works
* High level view of Transformer blocks and data flow
* What “tokens”, embeddings, and context length mean in practice

### Part 2: LLM Engineering Stack (Build + Deploy Skills)

Led by **Vansh Thakkar**

#### Phase 1: Efficient Fine Tuning (Quantization + LoRA + QLoRA)

You will learn the practical approach used in modern teams to fine tune models efficiently:

* What quantization is and why it matters (cost, speed, memory)
* LoRA: adapting models by training small adapters instead of all weights
* QLoRA: 4-bit quantized base + LoRA adapters for cheaper training
* Train adapters, save them, reload them, and run inference

#### Phase 2: Retrieval Augmented Generation (RAG) with Gemini + Chroma

You will learn how modern LLM apps connect to external knowledge:

* Build a knowledge base from PDFs (loading, chunking, embeddings)
* Store chunks with metadata (paper title, page number) in Chroma DB
* Retrieve top-k relevant chunks via similarity search
* Generate grounded answers using Gemini with retrieved context
* Improve retrieval for follow-ups using query rewriting + memory
* Lightweight sanity metrics for retrieval quality

---

## Repo structure

```text
.
├─ Spring26_Workshop_Part2_Phase1_QLoRA_PrivacyQA.ipynb
├─ Spring26_Workshop_Part2_Phase2_RAG_Gemini_Chroma.ipynb
├─ LLMs_Workshop_Spring26_Mar2_Part2.pptx
├─ documents/
│  ├─ attention_is_all_you_need.pdf
│  ├─ lora_pdf.pdf
│  ├─ rag_pdf.pdf
└─ chroma_db/               # created after running Phase 2 Part A
```

Notes:

* `documents/` must exist and contain the PDFs before running Phase 2 indexing.
* `chroma_db/` is created automatically when you run the indexing pipeline.

---

## Setup

### 1) Environment (recommended)

Use Python 3.10+ and a virtual environment.

```bash
python -m venv .venv
# Windows
.venv\Scripts\activate
# Mac/Linux
source .venv/bin/activate
```

### 2) Install dependencies

Both notebooks install what they need, but you can pre-install:

```bash
pip install -U langchain langchain-community langchain-google-genai chromadb pypdf python-dotenv
```

For the QLoRA notebook, training locally requires a CUDA-capable GPU setup.
If you don’t have that, run Phase 1 on Colab.

---

## API keys (Phase 2 RAG notebook)

Phase 2 uses **Gemini** via `langchain-google-genai`.

Set your key using either:

### Option A: Environment variable

**Windows (PowerShell)**

```powershell
setx GOOGLE_API_KEY "YOUR_KEY_HERE"
```

**Mac/Linux**

```bash
export GOOGLE_API_KEY="YOUR_KEY_HERE"
```

### Option B: `.env` file

Create a `.env` file in the repo root:

```text
GOOGLE_API_KEY=YOUR_KEY_HERE
```

---

## How to run

### Part 2, Phase 1: QLoRA Fine Tuning

Open and run:

* `Spring26_Workshop_Part2_Phase1_QLoRA_PrivacyQA.ipynb`

Flow:

1. Quantization theory (slides in markdown)
2. Load model in 4-bit
3. LoRA + QLoRA theory
4. Fine tune adapters
5. Save adapters
6. Reload adapters and run inference
7. (Optional) merge adapters into base model (commented)

### Part 2, Phase 2: RAG with Gemini + Chroma

Open and run:

* `Spring26_Workshop_Part2_Phase2_RAG_Gemini_Chroma.ipynb`

Flow:
**Part A (Indexing pipeline)**

1. Load PDFs from `documents/`
2. Chunk documents
3. Create embeddings and store in Chroma with metadata
4. Persist DB to `chroma_db/`

**Part B (RAG pipeline)**

1. Define queries (3 base + follow-ups commented)
2. Rewrite query (for retrieval only)
3. Retrieve top-k chunks (k=5) and print source + page
4. Build prompt with memory + retrieved context
5. Call Gemini and print grounded response
6. Update memory for follow-up questions
7. Run lightweight retrieval sanity checks

---

## Notes on evaluation (quick and practical)

This workshop prioritizes workflow + intuition:

* Before vs after behavior change (fine tuning)
* Better retrieval and grounded answers (RAG)

Lightweight sanity checks included:

* **Hit@K**: expected source appears in top-k retrieval
* **MRR@K**: how early the first expected source appears
* **Citation presence**: whether answers include source tags

These are quick checks, not a full benchmark.

---

## Troubleshooting

### RESOURCE_EXHAUSTED / Rate limit

Gemini can rate-limit. Add sleep between calls:

```python
time.sleep(1.5)
```

### Chroma DB already exists

If the DB already contains chunks, skip re-indexing to avoid duplicates.

### Follow-up query retrieval feels off

Make sure query rewriting uses conversation memory and is applied **only for retrieval**, not for final answer generation.

---

## Credits

**Dev Desai:** Part 1 (Transformers and LLM fundamentals)
**Vansh Thakkar:** Part 2 (Quantization + LoRA/QLoRA fine tuning, and RAG + improvements)
