# RAG Assignment – SEC 10-K Question Answering

This repository contains a Retrieval-Augmented Generation (RAG) system designed to answer factual questions from Apple and Tesla SEC Form 10-K filings. The system retrieves relevant document chunks, optionally re-ranks them, and generates document-grounded answers using an open-source large language model.

The solution is implemented as a runnable Google Colab notebook.

---

## Repository Structure

rag_assignment/
├── design_report.md # Concept note describing system design
├── requirements.txt # Python dependencies
├── rag_assignment.ipynb # Runnable Colab notebook
└── README.md


---

## System Overview

The RAG pipeline consists of the following steps:

1. **PDF Parsing**
   - Apple and Tesla 10-K PDFs are uploaded in Colab
   - Text is extracted page by page using `pypdf`

2. **Chunking**
   - Each page is split into overlapping text chunks
   - Chunk size and overlap are chosen to preserve context
   - Page-level metadata is retained for citations

3. **Embedding & Indexing**
   - Chunks are embedded using `BAAI/bge-small-en-v1.5`
   - FAISS is used to perform vector similarity search

4. **Re-ranking**
   - Retrieved chunks are re-ranked using `BAAI/bge-reranker-base`
   - Improves relevance for numerical and factual queries

5. **Answer Generation**
   - Uses `mistralai/Mistral-7B-Instruct-v0.2`
   - Loaded with 4-bit quantization for Colab compatibility
   - Prompt enforces strict document-grounded answers

6. **Out-of-Scope Handling**
   - If the answer is not present in the documents, the system returns:
     > *"This question cannot be answered based on the provided documents."*

---

## Running the Notebook (Google Colab)

1. Open the notebook in Google Colab  
2. Run the first cell to install dependencies  
3. Upload the following files when prompted:
   - `apple_doc.pdf`
   - `tesla_doc.pdf`
4. Run all cells to:
   - Build the vector index
   - Answer the evaluation questions
   - Generate final JSON output

---

## Live Notebook

👉 **Colab Notebook Link:**  
*(Add your public Colab link here before submission)*

---

## Design Report

A short concept note describing:
- Chunking strategy
- Embedding and LLM choices
- Re-ranking approach
- Out-of-scope handling

is available in [`design_report.md`](design_report.md).

---

## Dependencies

All required Python libraries are listed in `requirements.txt`.

---

## Notes & Limitations

- Financial tables are parsed via text extraction and may be sensitive to formatting.
- The system prioritizes correctness and document grounding over speculative answers.
- No external knowledge is used beyond the provided SEC filings.

---

## Author

Asmath Shaik
