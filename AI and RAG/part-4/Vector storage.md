# Vector Storage

### Simple Definition

**Vector storage** is the process of storing **embeddings (vectors)** along with related information so they can be searched and retrieved later.

In a RAG system, document chunks are converted into embeddings and stored in a **vector database**.

> **Vector storage = Store embeddings + original content/reference + metadata for efficient similarity search.**

---

## What Is Stored?

A vector record commonly contains:

- **ID** — Unique identifier
- **Embedding / Vector** — Numerical representation
- **Document / Chunk** — Original text or a reference to it
- **Metadata** — Additional information about the content

Conceptually:

    {
      id: "chunk_001",

      vector: [0.12, -0.45, 0.78, ...],

      document: "React is a JavaScript library.",

      metadata: {
        source: "react-notes.pdf",
        page: 10,
        category: "frontend"
      }
    }

The exact structure depends on the vector database.

---

## Vector Storage in RAG

During the indexing phase:

    Documents
         ↓
    Chunking
         ↓
    Generate Embeddings
         ↓
    Store Vectors + Metadata
         ↓
    Vector Database

For example:

    Chunk 1
    "React is a JavaScript library."
         ↓
    Embedding Model
         ↓
    [0.12, -0.45, 0.78, ...]
         ↓
    Store in Vector Database

---

## Why Store Metadata?

Metadata provides additional information about a vector.

For example:

    {
      department: "HR",
      document_type: "policy",
      year: 2026
    }

It can be used to filter search results.

For example:

    User Query
         ↓
    Similarity Search

    + Filter:
    department = "HR"

         ↓
    Relevant HR Documents

This helps improve the relevance of retrieved results.

---

## How Retrieval Works

When a user asks a question:

    User Question
         ↓
    Generate Query Embedding
         ↓
    Search Stored Vectors
         ↓
    Compare Similarity
         ↓
    Retrieve Top-K Vectors
         ↓
    Get Associated Document Chunks
         ↓
    Send Context to LLM

The vector database does not usually return just a vector. It can also return the associated document or reference and metadata.

---

## Common Vector Storage Technologies

Examples include:

- ChromaDB
- Pinecone
- Weaviate
- Milvus

FAISS can also store vectors in an index for similarity search, but it is primarily a vector similarity search library rather than a complete vector database.

---

## Important Point

The **embedding represents the meaning of the text**, but the embedding itself is not useful to directly read as context.

For example:

    [0.12, -0.45, 0.78, ...]

cannot be directly understood by a user or used as readable RAG context.

So the system typically stores or maintains a connection between:

    Embedding
        ↕
    Original Text / Chunk
        ↕
    Metadata

This allows the system to retrieve the vector and then access the relevant original content.

---

## Vector Storage vs Normal Storage

### Normal Database

Stores structured or regular data:

    Users
    ├── ID
    ├── Name
    └── Email

### Vector Storage

Stores embeddings and related information:

    Vector Record
    ├── ID
    ├── Embedding
    ├── Document / Content
    └── Metadata

---

## Interview Answer

If the interviewer asks:

**"What is vector storage?"**

You can answer:

> **"Vector storage is the process of storing embeddings along with their IDs, associated document chunks or references, and metadata. In a RAG system, document chunks are converted into embeddings and stored in a vector database. Later, a user's query is converted into an embedding and compared with the stored vectors to retrieve the most relevant document chunks."**

---

## Short Version to Remember

    Document Chunk
         ↓
    Embedding Model
         ↓
    Vector
         ↓
    Store:
    - ID
    - Vector
    - Text / Reference
    - Metadata
         ↓
    Vector Database

**Vector Storage = Store embeddings and their related information for similarity search.**