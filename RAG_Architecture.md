# RAG (Retrieval-Augmented Generation) Architecture

## Quick Overview

RAG works in **two major stages**:

1. **Data Ingestion** – Preparing and storing knowledge.
2. **Retrieval & Generation** – Finding relevant information and generating the final answer.

---

# Stage 1: Data Ingestion

We start with the **data** (the knowledge base).

Since Large Language Models have a limited **context window (the maximum amount of text/tokens an LLM can process in a single request)**, we cannot feed an entire document into the model.

Instead, we first divide the data into multiple **chunks (small, meaningful pieces of a larger document that can be processed independently)**.

After chunking, each chunk is converted into an **embedding (a high-dimensional numerical representation of text that captures its semantic meaning instead of just keywords)**.

These embeddings are then stored inside a **vector database (a specialized database designed to efficiently store and search embeddings using similarity instead of exact matching)**.

Once all embeddings are stored, **Stage 1 (Data Ingestion)** is complete.

---

# Stage 2: Retrieval & Generation

When a user asks a question:

1. The user's query is converted into an **embedding (a numerical representation of the query in the same vector space as the stored document embeddings)**.
2. The vector database performs a **semantic search (search based on meaning rather than exact keyword matches)** or **similarity search (finding vectors that are mathematically closest to the query vector)**.
3. The database retrieves the **Top-K documents (the K most relevant chunks based on similarity score)**.

At this point, more advanced retrieval techniques can be applied.

---

## Hybrid Search

Instead of relying only on semantic search, many RAG systems use **Hybrid Search (a retrieval technique that combines semantic/vector search with traditional keyword-based search such as BM25 to improve accuracy)**.

This helps retrieve documents that are both:

- Semantically similar
- Keyword relevant

---

## Reranking

After retrieval, we may have the **Top-K retrieved documents (the initial set of most relevant chunks returned by the retrieval system)**.

However, these documents are not always ranked perfectly.

A **reranker (a model that re-evaluates the retrieved documents and produces a more accurate ranking based on the user's query)** is used to reorder these documents.

Instead of using only vector similarity, the reranker understands the relationship between the query and each retrieved document much more deeply.

As a result, the most relevant documents move to the top while less useful ones move down.

---

## Retrieval Techniques

After retrieval (and optionally reranking), different retrieval strategies can be applied, such as:

- **Top-K Retrieval (returning only the K highest-ranked documents)**.
- **Hybrid Retrieval (combining keyword and semantic retrieval)**.
- **Metadata Filtering (retrieving documents that satisfy specific metadata conditions like date, author, category, etc.)**.
- **Multi-Query Retrieval (generating multiple versions of the user's question to improve recall)**.
- **Parent-Child Retrieval (retrieving smaller chunks while preserving access to the larger parent document for better context)**.

---

# Final Generation

Finally, the selected documents (also called **context (the retrieved information provided to the LLM to help answer the user's question)**) are sent to the **LLM (Large Language Model—a neural network trained on massive amounts of text that can understand and generate human-like language)** along with the user's original query.

The LLM uses:

- The retrieved context
- The user's question
- Its own reasoning capabilities

to generate the final answer.

---

# Complete Flow

```text
Knowledge Base
      │
      ▼
Chunking
(Splitting documents into smaller pieces)
      │
      ▼
Embeddings
(Convert text into numerical vectors)
      │
      ▼
Vector Database
(Store embeddings)
      │
──────────── Stage 1 Complete ────────────
      │
      ▼
User Query
      │
      ▼
Query Embedding
      │
      ▼
Semantic / Similarity Search
      │
      ▼
(Optional) Hybrid Search
      │
      ▼
Top-K Retrieved Documents
      │
      ▼
(Optional) Reranker
      │
      ▼
Best Ranked Documents
      │
      ▼
LLM + Retrieved Context
      │
      ▼
Final Answer
```