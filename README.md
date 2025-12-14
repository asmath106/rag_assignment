# RAG Assignment – SEC 10-K Question Answering

This repository contains a Retrieval-Augmented Generation (RAG) system built to answer factual questions using Apple and Tesla SEC Form 10-K filings. The system retrieves relevant document context, re-ranks results, and generates grounded answers using an open-source large language model.

The solution is implemented as a runnable **Google Colab notebook**.

---

## Repository Structure

rag_assignment/
├── design_report.md # Concept note describing system design
├── requirements.txt # Python dependencies
├── rag_assignment_final.ipynb # Runnable Colab notebook
└── README.md



---

## System Architecture

The RAG pipeline consists of the following stages:

### 1. PDF Ingestion
- Apple and Tesla 10-K PDFs are uploaded interactively in Colab.
- Text is extracted page-by-page using `pypdf`.

### 2. Chunking
- Pages are split into overlapping chunks (1000 chars, 150 overlap).
- Page-level metadata is preserved for citation purposes.

### 3. Embedding & Vector Search
- Chunk embeddings generated using `BAAI/bge-small-en-v1.5`.
- FAISS `IndexFlatL2` is used for similarity search.
- Top 15 candidate chunks retrieved per query.

### 4. Re-ranking
- Retrieved chunks are re-ranked using `BAAI/bge-reranker-base`.
- Top 5 chunks are selected for answer generation.

### 5. Answer Generation
- Uses `mistralai/Mistral-7B-Instruct-v0.2` with 4-bit quantization.
- Strict prompt enforces document-only answers with citations.
- Deterministic generation reduces hallucination.

### 6. Out-of-Scope Handling
If an answer is not present in the provided documents, the system returns:
> **"This question cannot be answered based on the provided documents."**

---

## Running the Notebook (Google Colab)

1. Open the notebook in Google Colab
2. Run Cell 1 to install dependencies
3. Upload:
   - `apple_doc.pdf`
   - `tesla_doc.pdf`
4. Run all cells to:
   - Build the FAISS index
   - Answer evaluation questions
   - Generate final JSON output

---

## Live Notebook Link

👉 **Colab Notebook:**  
(https://colab.research.google.com/drive/1mQHNp_0UkOAkRuen8sP431HG-aRe6asp#scrollTo=ULPYRAkvO1XB)

---

## Design Documentation

Detailed system design decisions are documented in  
📄 [`design_report.md`](design_report.md)

---

## Notes & Limitations
- Financial tables are parsed as raw text.
- Numerical precision depends on PDF extraction quality.
- No external data or tools are used beyond the provided filings.

---

## Author
**Asmath Shaik**

