# MQ-CRAG

A self-validating Corrective RAG (CRAG) system with Multi-Query Retrieval (MQR), built as a semester research project exploring Generative AI and Explainable AI concepts in retrieval-augmented generation.

## Problem statement

Standard RAG pipelines assume retrieved documents are relevant to the query — an assumption that frequently fails in practice, leading to hallucinated or off-topic answers. MQ-CRAG addresses this by combining:

- **Multi-Query Retrieval (MQR)** — decomposing a single query into multiple sub-queries to improve recall, especially for multi-hop questions
- **Self-validation** — a relevance grading step that scores retrieved chunks before they reach the generator
- **Corrective retrieval** — a fallback path (query rewrite / web search) triggered when retrieved context is judged insufficient

The goal is to reduce hallucination and improve answer grounding compared to both vanilla RAG and single-query CRAG, while providing an explainable trail of *why* a chunk was accepted or rejected.

## Pipeline overview

1. **Query intake** — user question enters the system
2. **Multi-query generator** — an LLM produces 3–5 reformulated/decomposed sub-queries
3. **Retriever** — dense retrieval per sub-query against a vector index, merged and deduplicated
4. **Relevance grader** — scores each retrieved chunk against the original query (LLM-as-judge and/or cross-encoder)
5. **Corrective controller** — routes to the generator directly if relevance is sufficient, or to a corrective retry (query rewrite / web search fallback) if not
6. **Generator** — synthesizes the final answer from validated context only


## Tech stack

- **Orchestration:** LangGraph (models the pipeline as a graph with a conditional edge for the corrective branch)
- **LLM:** Ollama (`qwen3.5:4b` for query generation and answer synthesis), with a hosted API model (e.g. Gemini 2.5 Flash-Lite) as an alternative grader backend for comparison
- **Vector store:** FAISS or ChromaDB
- **Cross-encoder grader:** `sentence-transformers` (e.g. `ms-marco-MiniLM-L-6-v2`)

## Citation context

Builds on Yan et al., *Corrective Retrieval Augmented Generation* (arXiv:2401.15884, 2024). Full literature survey in progress.
