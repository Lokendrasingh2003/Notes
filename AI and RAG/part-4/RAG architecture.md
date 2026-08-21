# RAG Architecture

### Simple Definition

**RAG architecture** describes how a RAG system processes documents, retrieves relevant information, and provides that information to an LLM to generate an answer.

A typical RAG system has **two main phases**:

1. **Indexing / Ingestion Phase** — Prepare and store the documents
2. **Retrieval / Generation Phase** — Retrieve relevant information and generate an answer

---

## Complete RAG Architecture

    ┌─────────────────────────────────────────────────────┐
    │              1. INDEXING / INGESTION                │
    └─────────────────────────────────────────────────────┘

    Documents
    PDF / DOCX / Website / Database
              ↓
    Text Extraction
              ↓
    Chunking
              ↓
    Embedding Model
              ↓
    Embeddings / Vectors
              ↓
    Vector Database


    ┌─────────────────────────────────────────────────────┐
    │           2. RETRIEVAL / GENERATION                 │
    └─────────────────────────────────────────────────────┘

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
    Build Prompt with Context
              ↓
    LLM
              ↓
    Final Answer

---

# 1. Indexing / Ingestion Phase

This phase happens **before users ask questions**.

Its purpose is to process documents and make them searchable.

## Step 1: Load Documents

The application collects information from different sources:

- PDF files
- DOCX files
- Websites
- Databases
- Internal company documents
- APIs

Example:

    Company Employee Policy.pdf

---

## Step 2: Extract Text

The application extracts the useful text from the document.

    PDF
     ↓
    Text Extraction
     ↓
    "Employees receive 20 days of paid leave..."

---

## Step 3: Chunking

Large documents are split into smaller pieces called **chunks**.

    Large Document
           ↓
        Chunking
           ↓
    ┌─────────┬─────────┬─────────┐
    │ Chunk 1 │ Chunk 2 │ Chunk 3 │
    └─────────┴─────────┴─────────┘

Chunking is important because sending an entire large document to an LLM is inefficient and may exceed the context window.

---

## Step 4: Generate Embeddings

Each chunk is converted into an embedding using an embedding model.

    Chunk
      ↓
    Embedding Model
      ↓
    Vector

Example:

    "Employees receive 20 days of paid leave."
                    ↓
    [0.12, -0.45, 0.78, ...]

---

## Step 5: Store in Vector Database

The embeddings are stored in a vector database along with useful information.

    {
      id: "chunk_1",
      vector: [0.12, -0.45, 0.78, ...],
      text: "Employees receive 20 days of paid leave.",
      metadata: {
        source: "leave-policy.pdf"
      }
    }

Common vector databases include:

- ChromaDB
- Pinecone
- Weaviate
- Milvus

FAISS can also be used for efficient vector similarity search.

---

# 2. Retrieval / Generation Phase

This phase happens when the user asks a question.

## Step 1: User Sends a Question

Example:

    "How many paid leaves do employees get?"

---

## Step 2: Generate Query Embedding

The user's question is converted into a vector using an embedding model.

    User Question
          ↓
    Embedding Model
          ↓
    Query Vector

---

## Step 3: Search the Vector Database

The query vector is compared with the stored document vectors.

    Query Vector
          ↓
    Vector Database
          ↓
    Similarity Search
          ↓
    Most Relevant Chunks

Similarity can be measured using:

- Cosine similarity
- Dot product
- Euclidean distance

---

## Step 4: Retrieve Top-K Chunks

The system retrieves the most relevant chunks.

Example:

    Chunk A → 0.94
    Chunk B → 0.87
    Chunk C → 0.42

If:

    K = 2

Then:

    Chunk A
    Chunk B

are selected.

---

## Step 5: Build the Prompt

The retrieved chunks are added to the prompt as context.

Conceptually:

    System:
    Answer the question using only the provided context.

    Context:
    Employees receive 20 days of paid leave every year.

    User Question:
    How many paid leaves do employees get?

---

## Step 6: LLM Generates the Answer

The LLM receives:

    Prompt
    + Retrieved Context
    + User Question

It then generates the final answer:

    "Employees receive 20 days of paid leave every year."

---

# Complete RAG Flow

    ┌──────────────────┐
    │    Documents     │
    └────────┬─────────┘
             ↓
    ┌──────────────────┐
    │ Text Extraction  │
    └────────┬─────────┘
             ↓
    ┌──────────────────┐
    │     Chunking     │
    └────────┬─────────┘
             ↓
    ┌──────────────────┐
    │ Embedding Model  │
    └────────┬─────────┘
             ↓
    ┌──────────────────┐
    │ Vector Database  │
    └────────┬─────────┘
             ↓
    ┌──────────────────┐
    │ Similarity Search│ ← User Query
    └────────┬─────────┘
             ↓
    ┌──────────────────┐
    │ Top-K Chunks     │
    └────────┬─────────┘
             ↓
    ┌──────────────────┐
    │ Prompt + Context │
    └────────┬─────────┘
             ↓
    ┌──────────────────┐
    │       LLM        │
    └────────┬─────────┘
             ↓
    ┌──────────────────┐
    │   Final Answer   │
    └──────────────────┘

---

# Important Components

A basic RAG architecture includes:

| Component | Purpose |
|---|---|
| Document Loader | Loads data from different sources |
| Text Extractor | Extracts usable text |
| Chunker | Splits documents into smaller pieces |
| Embedding Model | Converts text into vectors |
| Vector Database | Stores and searches embeddings |
| Retriever | Finds relevant chunks |
| Prompt Builder | Combines context with the user question |
| LLM | Generates the final answer |

---

# Simple Example

Suppose a company has thousands of internal documents.

The user asks:

    "What is the leave policy?"

The system does:

    User Question
          ↓
    Convert Question to Embedding
          ↓
    Search Vector Database
          ↓
    Retrieve Leave Policy Chunks
          ↓
    Add Chunks to Prompt
          ↓
    LLM
          ↓
    Generate Answer

The system does **not** send thousands of documents directly to the LLM.

It retrieves only the most relevant information.

---

# Interview Answer

If the interviewer asks:

**"Explain the architecture of a RAG system."**

You can answer:

> **"A RAG system has two main phases: indexing and retrieval. In the indexing phase, we load documents, extract the text, split it into chunks, convert the chunks into embeddings, and store them in a vector database. In the retrieval phase, the user's question is converted into an embedding and used to perform similarity search. The Top-K relevant chunks are retrieved and added as context to the prompt. Finally, the LLM uses the question and retrieved context to generate the answer."**

---

# Short Version to Remember

    INDEXING:

    Documents
        ↓
    Extract Text
        ↓
    Chunking
        ↓
    Embeddings
        ↓
    Vector Database


    RETRIEVAL:

    User Question
        ↓
    Query Embedding
        ↓
    Similarity Search
        ↓
    Top-K Chunks
        ↓
    Prompt + Context
        ↓
    LLM
        ↓
    Answer

**RAG Architecture = Index documents first → Retrieve relevant context → Give context to LLM → Generate answer**