# Hybrid Search

### Simple Definition

**Hybrid search** combines **keyword search** and **semantic/vector search** to retrieve more relevant information.

In simple words:

> **Hybrid Search = Keyword Search + Semantic Search**

It is commonly used in **RAG systems** to improve retrieval accuracy.

---

## Why Is Hybrid Search Needed?

Semantic search is good at understanding **meaning**, while keyword search is good at finding **exact terms**.

For example, suppose the user asks:

    "What is the error code ERR-404 in the payment service?"

A semantic search may understand the meaning of the question, but an exact keyword search is useful for finding the specific term:

    ERR-404

Using both approaches can produce better results.

---

## Keyword Search

Keyword search looks for exact or closely matching words.

Example:

    Query:
    "React authentication"

It may retrieve documents containing:

    "React authentication"

    "React Auth"

    "authentication in React"

Traditional keyword search commonly uses techniques such as:

- BM25
- TF-IDF

---

## Semantic Search

Semantic search uses **embeddings** to find documents based on meaning.

    User Query
         ↓
    Embedding Model
         ↓
    Query Vector
         ↓
    Vector Database
         ↓
    Similarity Search
         ↓
    Relevant Documents

Example:

    Query:
    "How do I protect a React application?"

It may retrieve:

    "React applications can use authentication
    and authorization to protect routes."

Even though the exact words are different, the meanings are related.

---

## Hybrid Search

Hybrid search combines both approaches.

    User Query
         ↓
    ┌───────────────────────┐
    │                       │
    ↓                       ↓
Keyword Search       Semantic Search
    │                       │
    ↓                       ↓
Keyword Results      Vector Results
    │                       │
    └───────────┬───────────┘
                ↓
         Combine / Rerank
                ↓
        Final Top-K Results
                ↓
               LLM
                ↓
             Answer

---

## Example

Suppose the user asks:

    "What is the ERR-404 payment error?"

The system performs:

### Keyword Search

Finds documents containing:

    ERR-404

### Semantic Search

Finds documents related to:

    Payment errors
    Failed transactions
    Payment service issues

The results are combined.

    Keyword Results
          +
    Semantic Results
          ↓
    Combined Results
          ↓
    Reranking
          ↓
    Top-K Relevant Chunks
          ↓
    LLM
          ↓
    Answer

---

## Why Hybrid Search Is Better

Hybrid search can handle both:

### Exact Matching

Useful for:

- Error codes
- Product IDs
- Names
- Technical terms
- API endpoints
- Version numbers

Example:

    "ERR-404"
    "API_v2"
    "PROD-123"

### Semantic Matching

Useful for:

- Natural language questions
- Similar meanings
- Paraphrased questions
- Concept-based searches

Example:

    "How can I protect my application?"

can match:

    "Authentication and authorization are used
    to secure web applications."

---

## Hybrid Search in RAG

A typical RAG pipeline can be:

    Documents
         ↓
    Chunking
         ↓
    ┌─────────────────────┐
    │                     │
    ↓                     ↓
Keyword Index       Embedding Model
    │                     │
    ↓                     ↓
Keyword Search      Vector Search
    │                     │
    └──────────┬──────────┘
               ↓
         Combine Results
               ↓
            Rerank
               ↓
          Top-K Chunks
               ↓
        Prompt + Context
               ↓
              LLM
               ↓
            Answer

---

## Hybrid Search vs Semantic Search

| Hybrid Search | Semantic Search |
|---|---|
| Keyword + semantic search | Uses semantic/vector search |
| Handles exact terms well | Handles meaning well |
| Handles semantic meaning | May miss exact identifiers |
| Often better for technical documents | Useful for natural-language queries |

---

## Example in a Technical RAG System

Suppose you have documentation containing:

    API endpoint:
    /api/v2/users/123

User asks:

    "What does /api/v2/users/123 return?"

Keyword search is useful because the exact endpoint is important.

At the same time, semantic search can find documentation explaining what the endpoint does.

Hybrid search combines both results to improve retrieval.

---

## Interview Answer

If the interviewer asks:

**"What is hybrid search in RAG?"**

You can answer:

> **"Hybrid search combines keyword-based search with semantic vector search. Keyword search is useful for exact terms such as error codes, product IDs, and technical names, while semantic search is useful for understanding the meaning of a query. The results from both searches can be combined and reranked to retrieve more relevant document chunks for the LLM."**

---

## Short Version to Remember

    User Query
         ↓
    ┌───────────────┐
    ↓               ↓
Keyword Search   Vector Search
    ↓               ↓
    └───────┬───────┘
            ↓
      Combine Results
            ↓
          Rerank
            ↓
        Top-K Chunks
            ↓
           LLM
            ↓
          Answer

**Hybrid Search = Keyword Search + Semantic Search**

**Keyword Search → Exact terms**

**Semantic Search → Meaning**

**Hybrid Search → Best of both**