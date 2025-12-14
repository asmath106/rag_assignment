# RAG Assignment – Design Report

## Overview
This project implements a Retrieval-Augmented Generation (RAG) pipeline to answer factual questions from two SEC Form 10-K filings (Apple and Tesla). The system retrieves relevant document chunks using vector similarity search, optionally re-ranks them, and generates answers constrained to the retrieved context.

The solution is implemented as a runnable Google Colab notebook and follows a modular, step-wise RAG design.

---

## Chunking Strategy
- PDFs are parsed page by page using `pypdf`.
- Each page is split into overlapping text chunks.
- Chunk size: **~1000 characters**
- Chunk overlap: **~150 characters**

This approach preserves table context while avoiding overly large chunks that could dilute retrieval precision. Page-level metadata is preserved to enable citation tracking.

---

## Embedding & Retrieval
- **Embedding model:** `BAAI/bge-small-en-v1.5`
- All chunks are embedded and indexed using **FAISS (IndexFlatL2)**.
- Query embeddings are matched against the FAISS index to retrieve top-K relevant chunks.

This lightweight embedding model was chosen to balance accuracy and Colab memory constraints.

---

## Re-ranking
- A cross-encoder re-ranker (`BAAI/bge-reranker-base`) is applied to the retrieved chunks.
- Re-ranking improves relevance by scoring `(query, chunk)` pairs.
- The top re-ranked chunks are passed to the LLM as context.

---

## LLM Choice
- **Model:** `mistralai/Mistral-7B-Instruct-v0.2`
- Loaded with **4-bit quantization (BitsAndBytes)** to run within Colab limits.
- Generation is run with `do_sample=False` for deterministic answers.

The model is prompted with strict instructions to:
- Answer only from provided context
- Return concise answers
- Avoid hallucination

---

## Out-of-Scope Handling
The system explicitly returns **“This question cannot be answered based on the provided documents.”** when:
- The answer does not exist in the 10-K filings
- The question asks about future predictions or opinions (e.g., stock price forecast)
- The context does not contain sufficient evidence

This behavior is enforced via prompt rules and post-processing logic.

---

## Limitations
- Financial table parsing relies on text extraction and may miss complex tabular structures.
- Some numerical aggregation tasks remain sensitive to chunk boundaries.
- The system prioritizes correctness and traceability over full automation.

---

## Conclusion
This implementation demonstrates a practical RAG pipeline using open-source models, vector search, and document-grounded generation. It emphasizes transparency, citation traceability, and robust handling of unanswerable questions.
