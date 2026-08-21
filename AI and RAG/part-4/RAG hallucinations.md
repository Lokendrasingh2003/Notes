# RAG Hallucinations

### Simple Definition

**RAG hallucination** occurs when an LLM generates an **incorrect, unsupported, or made-up answer**, even though the application uses retrieved information from external sources.

In simple words:

> **RAG reduces hallucinations by providing relevant context to the LLM, but it does not completely eliminate them.**

---

## Why Can Hallucinations Happen in RAG?

RAG hallucinations can happen because of problems in:

- Retrieval
- Chunking
- Embeddings
- Prompt design
- LLM generation
- Missing information

---

## 1. Poor Retrieval

If the system retrieves the wrong documents, the LLM receives incorrect context.

    User Question
         ↓
    Vector Search
         ↓
    Wrong Documents
         ↓
    LLM
         ↓
    Incorrect Answer

Example:

User asks:

    "What is the company's leave policy?"

But the retriever returns:

    "Company work-from-home policy."

The LLM may generate an incorrect answer based on the retrieved context.

---

## 2. Poor Chunking

If documents are split incorrectly, important information can be separated.

Example:

    Chunk 1:
    "Employees receive 20 days of leave..."

    Chunk 2:
    "...only after completing one year."

If only Chunk 1 is retrieved, the LLM may miss an important condition.

Better chunking helps preserve related information.

---

## 3. Poor Embeddings

If the embedding model does not represent the meaning of the query and documents effectively, the system may retrieve irrelevant chunks.

    User Query
         ↓
    Poor Embedding
         ↓
    Wrong Similarity Results
         ↓
    Wrong Context
         ↓
    LLM
         ↓
    Incorrect Answer

---

## 4. Insufficient Context

The retrieved documents may not contain enough information to answer the question.

Example:

    User:
    "Who approved this policy?"

Retrieved document:

    "The company introduced a new leave policy."

The document does not mention who approved it.

If the LLM tries to answer anyway, it may hallucinate.

---

## 5. Weak Prompt Instructions

A poorly designed prompt may allow the LLM to generate information that is not supported by the retrieved context.

A better prompt can instruct the model:

    "Answer only using the provided context.
     If the answer is not present in the context,
     say that you don't know."

This helps reduce unsupported answers.

---

# How to Reduce RAG Hallucinations

## 1. Improve Retrieval

Use better:

- Embedding models
- Chunking strategies
- Similarity search
- Metadata filtering
- Hybrid search
- Query transformation

---

## 2. Use Reranking

Retrieve multiple candidates and use a reranker to select the most relevant chunks.

    Initial Retrieval
         ↓
    Top-N Candidates
         ↓
    Reranker
         ↓
    Top-K Relevant Chunks
         ↓
    LLM

This can reduce irrelevant context.

---

## 3. Improve Chunking

Choose an appropriate:

- Chunk size
- Chunk overlap
- Chunking strategy

The goal is to keep related information together.

---

## 4. Improve the Prompt

Give the LLM clear instructions.

Example:

    System:
    Answer the question using only the provided context.

    If the answer cannot be found in the context,
    say "I don't have enough information to answer."

This helps prevent the model from inventing information.

---

## 5. Use Metadata Filtering

If the knowledge base contains different types of documents, metadata can restrict the search.

Example:

    department = "HR"

    document_type = "policy"

    year = 2026

This can prevent unrelated documents from being retrieved.

---

## 6. Use Citations / Sources

The application can show the sources used to generate the answer.

Example:

    Answer:
    Employees receive 20 days of paid leave.

    Source:
    employee-leave-policy.pdf
    Page 5

This makes the answer easier to verify.

---

## 7. Evaluate Retrieval Quality

You should evaluate whether the system is retrieving the correct information.

Important metrics include:

- Precision
- Recall
- Context relevance
- Answer relevance
- Faithfulness / groundedness

The goal is to check both:

    Retrieval Quality
          +
    Generation Quality

---

# RAG Hallucination Example

### Without Good Retrieval

    User:
    "What is the company's leave policy?"

    Retrieved Context:
    "Employees can work remotely two days per week."

    LLM:
    "Employees receive 25 days of annual leave."

The answer is unsupported by the retrieved context.

---

### With Better Retrieval

    User:
    "What is the company's leave policy?"

    Retrieved Context:
    "Employees receive 20 days of paid leave every year."

    LLM:
    "Employees receive 20 days of paid leave every year."

The answer is grounded in the retrieved context.

---

# Important Point

RAG does **not** guarantee that the LLM will always give a correct answer.

There are two major areas where problems can occur:

    Retrieval Problem
         ↓
    Wrong / Missing Context
         ↓
    LLM
         ↓
    Incorrect Answer

or:

    Correct Context
         ↓
    LLM
         ↓
    Unsupported / Incorrect Generation

Therefore, improving RAG quality requires improving both **retrieval and generation**.

---

# Interview Answer

If the interviewer asks:

**"Can RAG eliminate hallucinations?"**

You can answer:

> **"No. RAG can reduce hallucinations by providing the LLM with relevant external context, but it cannot completely eliminate them. Hallucinations can still occur if the system retrieves irrelevant or incomplete information, the documents are poorly chunked, or the LLM generates information that is not supported by the retrieved context. We can reduce them using better chunking, embeddings, hybrid search, reranking, metadata filtering, and prompts that instruct the model to answer only from the provided context."**

---

# Short Version to Remember

    Good Retrieval
         ↓
    Relevant Context
         ↓
    Strong Prompt
         ↓
    LLM
         ↓
    More Grounded Answer

**RAG reduces hallucinations but does not eliminate them.**

**Main goal: Ground the LLM's answer in reliable retrieved information.**