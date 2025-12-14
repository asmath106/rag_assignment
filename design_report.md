# Design Report – RAG System for SEC 10-K Question Answering

## Objective
The objective of this assignment is to build a Retrieval-Augmented Generation (RAG) system that answers factual questions using Apple and Tesla SEC Form 10-K filings. The system retrieves relevant document context and generates answers strictly grounded in the provided documents.

---

## Document Chunking Strategy
- PDFs are parsed page-by-page using `pypdf`.
- Each page is split into overlapping text chunks using a custom recursive splitter.
- Chunk size: **1000 characters**
- Overlap: **150 characters**
- Page-level metadata is preserved to support traceable citations.

This approach balances semantic coherence and retrieval accuracy while maintaining citation granularity.

---

## Embedding and Retrieval
- Text chunks are embedded using **BAAI/bge-small-en-v1.5**, a lightweight and effective embedding model.
- A **FAISS IndexFlatL2** vector index is used for fast similarity search.
- For each query, the top 15 candidate chunks are retrieved.

---

## Re-ranking
- Retrieved chunks are re-ranked using **BAAI/bge-reranker-base**.
- Re-ranking improves relevance, especially for numeric and financial questions where surface similarity alone is insufficient.
- The top 5 re-ranked chunks are used to construct the final context.

---

## Language Model Choice
- The system uses **Mistral-7B-Instruct-v0.2**, an open-source instruction-tuned LLM.
- The model is loaded with **4-bit quantization** using `bitsandbytes` to ensure compatibility with Google Colab GPUs.
- Deterministic decoding (`do_sample=False`) is used to reduce hallucination.

---

## Prompting and Answer Generation
- A strict prompt enforces:
  - Answers derived only from provided context
  - Single-line answers with inline citations
  - No explanations or external knowledge
- If the answer is not found in the context, the model is instructed to return **"Not answerable"**.

---

## Out-of-Scope Handling
The system explicitly handles:
- Opinion-based questions
- Future-looking questions (e.g., 2025 forecasts)
- Information not present in the filings

In these cases, the output is:
> "This question cannot be answered based on the provided documents."

---

## Limitations
- PDF text extraction quality can affect numerical accuracy.
- Complex financial tables are treated as plain text.
- No symbolic or rule-based post-processing is applied.

Despite these limitations, the system demonstrates a complete, reproducible RAG pipeline aligned with assignment requirements.
