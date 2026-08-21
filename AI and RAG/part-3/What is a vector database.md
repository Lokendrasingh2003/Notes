# What is a Vector Database?

### Simple Definition

A **Vector Database** is a database designed to **store, manage, and search vector embeddings efficiently**.

In AI and RAG applications, it is mainly used to find information that is **semantically similar** to a user's query.

In simple words:

> **A vector database stores embeddings and helps us quickly find the most relevant information based on meaning rather than just exact keywords.**

---

### Why Do We Need a Vector Database?

Suppose we have these documents:

    Document 1:
    "React is a JavaScript library."

    Document 2:
    "MongoDB is a NoSQL database."

    Document 3:
    "Node.js is a JavaScript runtime."

A user asks:

    "What is used to build user interfaces?"

A keyword search may not find the best result because the query doesn't contain the exact words:

    "React is a JavaScript library."

A vector database can compare the **embedding of the query** with the embeddings of the documents and find the document with the most similar meaning.

---

### How Does a Vector Database Work?

The basic flow is:

    Documents
         ↓
    Split into Chunks
         ↓
    Generate Embeddings
         ↓
    Store Embeddings
         ↓
    Vector Database

When a user asks a question:

    User Query
         ↓
    Generate Query Embedding
         ↓
    Search Vector Database
         ↓
    Similarity Search
         ↓
    Retrieve Relevant Chunks
         ↓
    LLM
         ↓
    Final Answer

---

### What Is Stored in a Vector Database?

A vector database can store:

- **Vector / Embedding**
- Original text or document reference
- Metadata
- IDs

For example:

    {
      id: "doc_001",
      vector: [0.12, -0.45, 0.78, ...],
      text: "React is a JavaScript library.",
      metadata: {
        source: "react-docs.pdf",
        page: 10
      }
    }

The exact structure depends on the vector database.

---

### Vector Database vs Normal Database

#### Normal Database

Traditional databases are commonly used for structured data.

Example:

    Users Table

    | id | name    | age |
    |----|---------|-----|
    | 1  | Rahul   | 25  |
    | 2  | Aman    | 28  |

You might search using:

    SELECT * FROM users
    WHERE name = 'Rahul';

This is mainly based on exact values or structured conditions.

---

#### Vector Database

A vector database stores numerical vectors.

Example:

    Document A → [0.12, 0.45, 0.78, ...]
    Document B → [0.32, 0.11, 0.64, ...]
    Document C → [0.91, 0.22, 0.13, ...]

It can perform:

    Query Vector
         ↓
    Similarity Search
         ↓
    Most Similar Vectors
         ↓
    Relevant Documents

---

### Vector Database in RAG

Vector databases are a major component of **RAG**.

The ingestion process:

    Documents
         ↓
    Text Extraction
         ↓
    Chunking
         ↓
    Embedding Model
         ↓
    Vector Embeddings
         ↓
    Vector Database

The retrieval process:

    User Question
         ↓
    Query Embedding
         ↓
    Vector Database
         ↓
    Similarity Search
         ↓
    Top-K Relevant Chunks
         ↓
    LLM
         ↓
    Answer

---

### Example

Suppose a company has a document:

    "Employees receive 20 days of paid leave every year."

During ingestion:

    Document
         ↓
    Chunk
         ↓
    Embedding
         ↓
    Vector Database

Later, the user asks:

    "How many paid leaves do employees get?"

The question is converted into an embedding.

The vector database searches for similar vectors and retrieves:

    "Employees receive 20 days of paid leave every year."

That retrieved information is then passed to the LLM.

    Retrieved Context
         ↓
    LLM
         ↓
    "Employees receive 20 days of paid leave every year."

---

### Similarity Search

A vector database uses a similarity metric to find relevant vectors.

Common similarity measures include:

- **Cosine similarity**
- **Euclidean distance**
- **Dot product**

The choice depends on the embedding model and application.

---

### Top-K Retrieval

Instead of retrieving every matching document, we usually retrieve the **Top-K most relevant results**.

For example:

    Query
      ↓
    Vector Search
      ↓
    Results:

    Document A → 0.92
    Document B → 0.87
    Document C → 0.81
    Document D → 0.32

If:

    K = 2

Then we retrieve:

    Document A
    Document B

These chunks are then provided to the LLM as context.

---

### Metadata Filtering

Vector databases can also store metadata and use it to filter results.

Example:

    Metadata:

    {
      "department": "HR",
      "year": 2026,
      "document_type": "policy"
    }

A search can be restricted to:

    department = "HR"

This helps retrieve more relevant information.

---

### Examples of Vector Databases / Vector Stores

Common technologies include:

- Pinecone
- FAISS
- Chroma
- Weaviate
- Milvus

**FAISS** is primarily a vector similarity search library rather than a full traditional database, but it is commonly used for building vector search systems.

---

### Vector Database vs Embeddings

These are different things.

**Embedding:**

> A numerical representation of information.

    Text
      ↓
    Embedding Model
      ↓
    Vector

**Vector Database:**

> A system that stores and searches those vectors efficiently.

    Vector
      ↓
    Vector Database
      ↓
    Similarity Search

---

### Interview Answer

If the interviewer asks:

**"What is a vector database?"**

You can answer:

> **"A vector database is a database designed to store and efficiently search vector embeddings. In RAG systems, documents are converted into embeddings and stored in a vector database. When a user asks a question, the query is also converted into an embedding, and similarity search is used to retrieve the most relevant document chunks. These chunks are then provided to the LLM as context to generate the answer."**

---

### Short Version to Remember

    Documents
         ↓
    Embeddings
         ↓
    Vector Database
         ↓
    Query Embedding
         ↓
    Similarity Search
         ↓
    Top-K Relevant Chunks
         ↓
    LLM
         ↓
    Answer

**Embedding = Vector representation**

**Vector Database = Stores and searches vectors**

**Similarity Search = Finds relevant vectors**