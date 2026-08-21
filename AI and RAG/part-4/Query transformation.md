# Query Transformation

### Simple Definition

**Query transformation** is the process of **rewriting or modifying a user's original query** into a better query before performing retrieval.

In simple words:

> **Query Transformation = Improve the user's query → Search for better results**

It is commonly used in **RAG systems** to improve retrieval quality.

---

## Why Is Query Transformation Needed?

Users may ask questions that are:

- Too short
- Ambiguous
- Missing important context
- Conversational
- Poorly worded
- Difficult for the retrieval system to understand

For example:

    User:
    "What about authentication?"

This question is unclear without previous conversation context.

A query transformation system might rewrite it as:

    "How does authentication work in the React and Node.js application?"

The improved query can then be used for retrieval.

---

## Basic Flow

    User Query
         ↓
    Query Transformation
         ↓
    Improved Query
         ↓
    Retrieval
         ↓
    Relevant Documents
         ↓
    LLM
         ↓
    Answer

---

# Common Query Transformation Techniques

## 1. Query Rewriting

The system rewrites the original query into a clearer version.

Example:

    Original:
    "How does it work?"

    Transformed:
    "How does RAG work in an AI application?"

The transformed query is more useful for retrieval.

---

## 2. Query Expansion

The system adds related terms to the query.

Example:

    Original:
    "React authentication"

    Expanded:
    "React authentication login authorization JWT protected routes"

This can help retrieve documents containing related terminology.

---

## 3. Query Decomposition

A complex question is broken into smaller questions.

Example:

    Original:
    "How does RAG work and how is it different from fine-tuning?"

The system may create:

    Query 1:
    "How does RAG work?"

    Query 2:
    "What is fine-tuning?"

    Query 3:
    "What is the difference between RAG and fine-tuning?"

Each query can be searched separately.

---

## 4. Multi-Query Retrieval

The system generates multiple versions of the same question.

Example:

    Original:
    "How can I improve RAG accuracy?"

Possible queries:

    "How to improve RAG retrieval accuracy?"

    "What techniques improve RAG performance?"

    "How can document retrieval be improved in RAG?"

Each query is searched, and the results are combined.

---

## 5. HyDE — Hypothetical Document Embeddings

**HyDE** stands for **Hypothetical Document Embeddings**.

Instead of directly embedding the user's question, the LLM first generates a hypothetical answer or document.

Example:

    User Query
         ↓
    LLM generates hypothetical answer
         ↓
    Generate embedding of hypothetical answer
         ↓
    Search Vector Database
         ↓
    Retrieve relevant documents

The idea is that a hypothetical answer may be semantically closer to the actual documents than the short user question.

---

# Example in RAG

Suppose the user asks:

    "Why is my RAG chatbot giving wrong answers?"

The query may be transformed into:

    "What are common causes of inaccurate answers in RAG systems,
     including poor chunking, incorrect retrieval, weak embeddings,
     and insufficient context?"

The improved query is then sent to the retrieval system.

    Transformed Query
          ↓
    Embedding Model
          ↓
    Vector Database
          ↓
    Similarity Search
          ↓
    Relevant Chunks
          ↓
    LLM
          ↓
    Answer

---

# Query Transformation with Conversation History

It is especially useful for conversational RAG.

Suppose:

    User:
    "What is RAG?"

    Assistant:
    Explains RAG.

Then the user asks:

    "What about fine-tuning?"

The second question is incomplete by itself.

Query transformation can use the conversation history:

    "What is the difference between RAG and fine-tuning?"

Now the transformed query is more meaningful for retrieval.

---

# Query Transformation vs Query Retrieval

These are different steps.

### Query Transformation

Improves the question.

    User Query
         ↓
    Rewrite / Expand / Decompose
         ↓
    Better Query

### Retrieval

Uses the query to find relevant information.

    Better Query
         ↓
    Vector / Keyword Search
         ↓
    Relevant Documents

So:

> **Query transformation improves the input to the retrieval system.**

---

# Query Transformation in RAG

A more advanced RAG pipeline can look like:

    User Question
         ↓
    Query Transformation
         ↓
    Improved Query
         ↓
    Hybrid / Vector Search
         ↓
    Candidate Documents
         ↓
    Reranking
         ↓
    Top-K Relevant Chunks
         ↓
    Prompt + Context
         ↓
    LLM
         ↓
    Final Answer

---

## Benefits

Query transformation can:

- Improve retrieval accuracy
- Handle ambiguous questions
- Improve conversational search
- Handle complex questions
- Find information using different query formulations
- Improve the quality of retrieved context

---

## Trade-Offs

Query transformation can also introduce:

- Additional LLM calls
- More latency
- Additional API cost
- Incorrect query rewrites

Therefore, it should be used when the improvement in retrieval quality justifies the additional complexity.

---

## Interview Answer

If the interviewer asks:

**"What is query transformation in RAG?"**

You can answer:

> **"Query transformation is the process of rewriting or modifying a user's query before retrieval to improve the quality of search results. It can involve query rewriting, expansion, decomposition, or generating multiple queries. For example, if a user asks an ambiguous question like 'What about authentication?', the system can use conversation history to transform it into a more specific query before searching the vector database."**

---

## Short Version to Remember

    User Query
         ↓
    Query Transformation
         ↓
    Better Query
         ↓
    Retrieval
         ↓
    Relevant Chunks
         ↓
    Reranking
         ↓
    LLM
         ↓
    Answer

**Query Transformation = Improve the query before retrieval.**

**Common techniques:**

- Query Rewriting
- Query Expansion
- Query Decomposition
- Multi-Query Retrieval
- HyDE