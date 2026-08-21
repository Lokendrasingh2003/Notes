# ChromaDB

### Simple Definition

**ChromaDB** is an open-source **vector database** designed for AI applications.

It is commonly used to **store embeddings and perform similarity searches**, especially in **RAG applications**.

In simple words:

> **ChromaDB stores document embeddings and helps retrieve documents that are semantically similar to a user's query.**

---

### Why Use ChromaDB?

In a RAG application, we need to:

- Store document embeddings
- Store document chunks
- Search for similar embeddings
- Retrieve relevant documents
- Store metadata

ChromaDB provides these capabilities.

---

### How ChromaDB Works

The basic flow is:

    Documents
         ↓
    Split into Chunks
         ↓
    Generate Embeddings
         ↓
    Store in ChromaDB

When the user asks a question:

    User Query
         ↓
    Generate Query Embedding
         ↓
    Search ChromaDB
         ↓
    Similarity Search
         ↓
    Retrieve Relevant Chunks
         ↓
    Send Context to LLM
         ↓
    Generate Answer

---

### Example

Suppose we have a document:

    "React is a JavaScript library for building user interfaces."

First, we create a chunk:

    Document Chunk
         ↓
    Embedding Model
         ↓
    Vector
         ↓
    ChromaDB

ChromaDB stores something conceptually like:

    {
      id: "doc_1",
      embedding: [0.12, -0.45, 0.78, ...],
      document: "React is a JavaScript library for building user interfaces.",
      metadata: {
        source: "react.pdf"
      }
    }

---

### Querying ChromaDB

User asks:

    "What is React used for?"

The query is converted into an embedding:

    User Query
         ↓
    Embedding Model
         ↓
    Query Vector
         ↓
    ChromaDB
         ↓
    Similarity Search
         ↓
    Relevant Document
         ↓
    LLM
         ↓
    Answer

The retrieved document can then be added to the LLM prompt as context.

---

### Collections

ChromaDB organizes data using **collections**.

A collection can contain:

- Documents
- Embeddings
- IDs
- Metadata

Conceptually:

    ChromaDB
       │
       ├── Collection: "company_docs"
       │      ├── Document 1
       │      ├── Document 2
       │      └── Document 3
       │
       └── Collection: "technical_docs"
              ├── Document 1
              ├── Document 2
              └── Document 3

Collections make it easier to organize related embeddings and documents.

---

### Metadata

ChromaDB can store metadata along with documents.

Example:

    {
      "source": "employee_policy.pdf",
      "department": "HR",
      "page": 10
    }

Metadata can help with filtering and identifying where retrieved information came from.

---

### ChromaDB in RAG

A typical RAG architecture using ChromaDB is:

    PDF / Documents
          ↓
    Text Extraction
          ↓
    Chunking
          ↓
    Embedding Model
          ↓
    ChromaDB
          ↓
    Store Embeddings + Documents + Metadata

Then:

    User Question
          ↓
    Query Embedding
          ↓
    ChromaDB Similarity Search
          ↓
    Top-K Relevant Chunks
          ↓
    Prompt + Retrieved Context
          ↓
    LLM
          ↓
    Final Answer

---

### ChromaDB vs FAISS

Both can be used for vector search, but they are not exactly the same.

| ChromaDB | FAISS |
|---|---|
| Vector database / vector store | Vector similarity search library |
| Stores embeddings and documents | Primarily focuses on vector search |
| Supports metadata | Metadata handling usually needs to be implemented separately |
| Easy to use for RAG prototypes | Very efficient for similarity search |
| Good for local AI applications | Commonly used for high-performance vector search |

For interview purposes:

> **ChromaDB is a convenient vector database/vector store for AI and RAG applications, while FAISS is primarily a library for efficient similarity search over vectors.**

---

### ChromaDB in an AI Application

For example, an AI interview application could store interview-related documents:

    Interview Questions
          ↓
    Chunk Documents
          ↓
    Generate Embeddings
          ↓
    Store in ChromaDB

When the user asks:

    "Give me React interview questions."

The application can:

    User Question
          ↓
    Query Embedding
          ↓
    ChromaDB
          ↓
    Retrieve Relevant Questions
          ↓
    LLM
          ↓
    Generate Response

---

### Interview Answer

If the interviewer asks:

**"What is ChromaDB?"**

You can answer:

> **"ChromaDB is an open-source vector database commonly used in AI and RAG applications. It allows us to store embeddings along with documents and metadata and perform similarity searches to retrieve relevant information. In a RAG pipeline, we can store document embeddings in ChromaDB and use the user's query embedding to retrieve the most relevant document chunks, which are then provided to the LLM as context."**

---

### Short Version to Remember

    Documents
         ↓
    Embeddings
         ↓
    ChromaDB
         ↓
    Similarity Search
         ↓
    Relevant Chunks
         ↓
    LLM
         ↓
    Answer

**ChromaDB = Vector database/vector store commonly used for AI and RAG applications.**