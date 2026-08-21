# Reranking

### Simple Definition

**Reranking** is the process of taking the initial search results and **reordering them based on their relevance to the user's query**.

In RAG, reranking is usually performed **after the initial retrieval and before sending the final context to the LLM**.

In simple words:

> **Reranking = Retrieve more candidates → Score their relevance more accurately → Select the best results**

---

## Why Is Reranking Needed?

The initial vector or keyword search may retrieve documents that are generally relevant but not necessarily the **most relevant**.

For example:

    User Query:
    "How do I reset my company email password?"

Initial search:

    Document A → 0.91
    Document B → 0.87
    Document C → 0.84
    Document D → 0.80

The scores from the initial retrieval may not perfectly represent the actual relevance.

A reranker examines the query and retrieved documents more carefully and may reorder them:

    Document C → 0.96
    Document A → 0.91
    Document D → 0.72
    Document B → 0.65

The system then sends the best results to the LLM.

---

## How Reranking Works

The basic flow is:

    User Query
         ↓
    Initial Retrieval
         ↓
    Retrieve Top-N Candidates
         ↓
    Reranker
         ↓
    Reorder by Relevance
         ↓
    Select Top-K Results
         ↓
    LLM
         ↓
    Answer

---

## Initial Retrieval vs Reranking

### Initial Retrieval

The retriever is designed to be **fast** and retrieve a larger set of potentially relevant documents.

For example:

    User Query
         ↓
    Vector / Keyword Search
         ↓
    Top-20 Documents

### Reranking

The reranker takes those 20 documents and determines which are **most relevant**.

    Top-20 Documents
         ↓
    Reranker
         ↓
    Top-5 Most Relevant Documents
         ↓
    LLM

This two-stage approach balances **speed and accuracy**.

---

## Example

User asks:

    "How can I reset my password?"

Initial retrieval returns:

    Document 1:
    "How to change your username"

    Document 2:
    "Password reset procedure"

    Document 3:
    "Account security guidelines"

    Document 4:
    "How to create an account"

The initial search may retrieve all four because they are related to accounts.

The reranker evaluates their relevance to the exact question:

    Password reset procedure → Highly Relevant
    Account security guidelines → Relevant
    Change username → Less Relevant
    Create account → Less Relevant

The final context might contain:

    1. Password reset procedure
    2. Account security guidelines

These are then sent to the LLM.

---

## Reranking in RAG

A typical RAG pipeline becomes:

    Documents
         ↓
    Chunking
         ↓
    Embeddings
         ↓
    Vector Database
         ↓
    Initial Retrieval
         ↓
    Top-N Candidates
         ↓
    Reranker
         ↓
    Top-K Relevant Chunks
         ↓
    Prompt + Context
         ↓
    LLM
         ↓
    Answer

---

## Why Retrieve Top-N First?

Suppose we ultimately need:

    Top-K = 5

Instead of retrieving only 5 documents initially, we might retrieve:

    Top-N = 20

Then the reranker evaluates those 20 documents and selects the best 5.

    1000 Documents
         ↓
    Initial Search
         ↓
    Top-20 Candidates
         ↓
    Reranking
         ↓
    Best 5
         ↓
    LLM

This gives the reranker more candidates to choose from.

---

## Reranking vs Similarity Search

These are different steps.

### Similarity Search

Finds documents that are similar to the query using embeddings or other search methods.

    Query
      ↓
    Vector Search
      ↓
    Candidate Documents

### Reranking

Takes those candidates and evaluates their relevance more deeply.

    Candidate Documents
          ↓
       Reranker
          ↓
    Best Documents

So:

> **Similarity search finds candidates.**

> **Reranking selects the most relevant candidates.**

---

## Reranking in Hybrid Search

Reranking is especially useful with **hybrid search**.

    User Query
         ↓
    ┌─────────────────┐
    ↓                 ↓
Keyword Search    Vector Search
    ↓                 ↓
    └────────┬────────┘
             ↓
      Combined Results
             ↓
          Reranker
             ↓
       Top-K Results
             ↓
            LLM
             ↓
           Answer

The reranker can help determine which results from both search methods are actually the most relevant.

---

## Benefits of Reranking

Reranking can:

- Improve retrieval accuracy
- Reduce irrelevant context
- Improve RAG answer quality
- Help select the best chunks
- Work with hybrid retrieval
- Reduce the amount of unnecessary context sent to the LLM

---

## Trade-Off

Reranking improves retrieval quality, but it adds:

- Additional computation
- Additional latency
- Additional cost in some implementations

Therefore, reranking is often used when **retrieval quality is more important than minimizing latency**.

---

## Interview Answer

If the interviewer asks:

**"What is reranking in RAG?"**

You can answer:

> **"Reranking is a second-stage retrieval process where we take the initial search results and reorder them based on their relevance to the user's query. Instead of sending all retrieved documents to the LLM, we can retrieve a larger set of candidates first, use a reranker to select the most relevant chunks, and then provide only the top results as context to the LLM. This can improve RAG retrieval accuracy."**

---

## Short Version to Remember

    Query
      ↓
    Initial Retrieval
      ↓
    Top-N Candidates
      ↓
    Reranker
      ↓
    Top-K Relevant Chunks
      ↓
    LLM
      ↓
    Answer

**Similarity Search → Finds candidates**

**Reranking → Reorders and selects the best candidates**

**Reranking = Better relevance before the LLM**