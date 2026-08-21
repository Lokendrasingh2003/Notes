# Similarity Search

### Simple Definition

**Similarity search** is the process of finding data that is **most similar to a given query**.

In AI and RAG applications, similarity search is usually performed on **embeddings (vectors)**.

In simple words:

> **Similarity search finds the most relevant information based on meaning rather than just exact keyword matching.**

---

### How Does Similarity Search Work?

The basic flow is:

    Query
      ↓
    Convert Query into Embedding
      ↓
    Compare Query Vector with Stored Vectors
      ↓
    Calculate Similarity
      ↓
    Rank Results
      ↓
    Retrieve Most Similar Results

---

### Example

Suppose we have these documents:

    Document A:
    "React is a JavaScript library for building user interfaces."

    Document B:
    "MongoDB is a NoSQL database."

    Document C:
    "Node.js is a JavaScript runtime."

The user asks:

    "What is used to build user interfaces?"

The query is converted into an embedding:

    User Query
         ↓
    [0.12, 0.45, 0.78, ...]

The documents also have embeddings:

    Document A → [0.11, 0.44, 0.80, ...]
    Document B → [0.75, 0.21, 0.14, ...]
    Document C → [0.30, 0.55, 0.20, ...]

The system compares the vectors.

Conceptually:

    Query ↔ Document A → 0.92
    Query ↔ Document B → 0.18
    Query ↔ Document C → 0.45

Document A has the highest similarity, so it is considered the most relevant result.

---

### Similarity Metrics

Common methods used to measure similarity or distance include:

- **Cosine similarity**
- **Dot product**
- **Euclidean distance**

For text embeddings, **cosine similarity** is commonly used.

---

### Similarity Search in RAG

Similarity search is a key part of the **RAG retrieval process**.

The complete flow is:

    Documents
         ↓
    Chunking
         ↓
    Generate Embeddings
         ↓
    Store Embeddings
         ↓
    Vector Database

When a user asks a question:

    User Question
         ↓
    Generate Query Embedding
         ↓
    Similarity Search
         ↓
    Retrieve Top-K Relevant Chunks
         ↓
    Add Chunks to Prompt
         ↓
    LLM
         ↓
    Final Answer

---

### Top-K Similarity Search

Usually, we don't retrieve every similar document.

Instead, we retrieve the **Top-K** most relevant results.

For example:

    Query
      ↓
    Similarity Search
      ↓
    Results:

    Document A → 0.94
    Document C → 0.88
    Document D → 0.81
    Document B → 0.32

If:

    K = 3

Then:

    Document A
    Document C
    Document D

are retrieved.

---

### Keyword Search vs Similarity Search

#### Keyword Search

Looks for matching words.

    Query:
    "How to create a React component?"

    Document:
    "React components can be created using functions."

Exact keyword matching may not always work well if the wording is different.

#### Similarity Search

Looks for **semantic meaning** using embeddings.

    Query
      ↓
    Query Embedding
      ↓
    Compare with Document Embeddings
      ↓
    Find Semantically Similar Documents

This allows the system to find relevant information even when the exact words are different.

---

### Similarity Search with Vector Databases

Vector databases are commonly used to perform similarity searches efficiently.

Examples:

- Pinecone
- ChromaDB
- Weaviate
- Milvus

FAISS can also perform efficient vector similarity search.

The general architecture is:

    Documents
         ↓
    Embeddings
         ↓
    Vector Database
         ↓
    Similarity Search
         ↓
    Relevant Documents

---

### Example in a Company RAG System

Suppose the company has thousands of documents.

User asks:

    "How many annual leaves do employees get?"

The system does not need to send all documents to the LLM.

Instead:

    User Question
         ↓
    Query Embedding
         ↓
    Similarity Search
         ↓
    Relevant Leave Policy Chunk
         ↓
    LLM
         ↓
    Answer

This makes RAG more efficient and helps provide relevant context to the LLM.

---

### Interview Answer

If the interviewer asks:

**"What is similarity search?"**

You can answer:

> **"Similarity search is the process of finding the most relevant or similar data to a given query. In AI applications, the query and stored documents are converted into embeddings, and a similarity metric such as cosine similarity is used to compare their vectors. In RAG, similarity search retrieves the Top-K most relevant document chunks, which are then provided to the LLM as context."**

---

### Short Version to Remember

    Query
      ↓
    Query Embedding
      ↓
    Compare with Stored Embeddings
      ↓
    Similarity Score
      ↓
    Rank Results
      ↓
    Top-K Relevant Chunks
      ↓
    LLM

**Similarity Search = Find the most semantically similar information.**