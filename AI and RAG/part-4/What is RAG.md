# What is RAG?

### Simple Definition

**RAG stands for Retrieval-Augmented Generation.**

RAG is a technique that allows an LLM to **retrieve relevant information from external data sources and use that information as context before generating an answer**.

In simple words:

> **RAG = Retrieve relevant information + Give it to the LLM + Generate an answer**

---

### Why Do We Need RAG?

An LLM may not have access to:

- Private company documents
- Internal databases
- Latest information
- Custom knowledge bases
- User-specific information

Instead of retraining the LLM, RAG allows us to provide the required information **at query time**.

For example:

    User:
    "What is our company's leave policy?"

The LLM may not know the company's private leave policy.

With RAG:

    User Question
         ↓
    Search Company Documents
         ↓
    Retrieve Relevant Information
         ↓
    Give Information to LLM
         ↓
    Generate Answer

---

### Basic RAG Architecture

    Documents
         ↓
    Text Extraction
         ↓
    Chunking
         ↓
    Embedding Model
         ↓
    Vector Database
         ↓
    Stored Embeddings


    User Question
         ↓
    Query Embedding
         ↓
    Similarity Search
         ↓
    Retrieve Relevant Chunks
         ↓
    Add Chunks to Prompt
         ↓
    LLM
         ↓
    Generated Answer

---

# RAG Has Two Main Phases

## 1. Indexing / Ingestion Phase

This happens before the user asks a question.

    Documents
         ↓
    Extract Text
         ↓
    Split into Chunks
         ↓
    Generate Embeddings
         ↓
    Store in Vector Database

For example:

    Company Policy PDF
         ↓
    Text
         ↓
    Chunks
         ↓
    Embeddings
         ↓
    ChromaDB / Pinecone

---

## 2. Retrieval / Generation Phase

This happens when the user asks a question.

    User Question
         ↓
    Generate Query Embedding
         ↓
    Search Vector Database
         ↓
    Retrieve Top-K Relevant Chunks
         ↓
    Add Retrieved Context to Prompt
         ↓
    LLM
         ↓
    Generated Answer

---

### Example

Suppose the company document contains:

> "Employees receive 20 days of paid leave every year."

The document is processed and stored in the vector database.

Later, the user asks:

    "How many paid leaves do employees get?"

The system performs:

    User Question
         ↓
    Query Embedding
         ↓
    Vector Search
         ↓
    Retrieve:
    "Employees receive 20 days of paid leave every year."
         ↓
    LLM
         ↓
    "Employees receive 20 days of paid leave every year."

The LLM uses the retrieved information to generate the answer.

---

### RAG vs Fine-Tuning

This is an important interview topic.

#### RAG

RAG provides external information to the LLM at **query time**.

    External Data
         ↓
    Retrieve Relevant Information
         ↓
    LLM
         ↓
    Answer

The model's parameters are **not changed**.

#### Fine-Tuning

Fine-tuning changes the model's parameters by training it further on specific data.

    Training Data
         ↓
    Fine-Tuning
         ↓
    Updated Model
         ↓
    Response

So:

> **RAG gives the model external context, while fine-tuning changes the model's learned parameters.**

---

### RAG and Hallucination

RAG can help reduce hallucinations by providing the LLM with relevant external information.

Without RAG:

    User Question
         ↓
    LLM
         ↓
    Possible Hallucination

With RAG:

    User Question
         ↓
    Retrieve Relevant Information
         ↓
    LLM + Retrieved Context
         ↓
    More Grounded Answer

However:

> **RAG does not completely eliminate hallucinations.**

If the retrieval system returns incorrect or irrelevant information, the LLM can still generate an incorrect answer.

---

### RAG Components

A basic RAG system contains:

1. **Documents** — Source of external knowledge
2. **Chunking** — Splits documents into smaller pieces
3. **Embedding Model** — Converts text into vectors
4. **Vector Database** — Stores and searches embeddings
5. **Retriever** — Finds relevant chunks
6. **LLM** — Generates the final answer
7. **Prompt** — Combines the user question with retrieved context

---

### RAG in a Real Application

For example, an AI interview application could contain:

    Interview Questions
    Interview Guides
    Company Information
    Technical Documentation
         ↓
    RAG Pipeline
         ↓
    Vector Database


    User:
    "Give me React questions based on our interview material."
         ↓
    Retrieve Relevant Content
         ↓
    LLM
         ↓
    Personalized Answer

---

### Interview Answer

If the interviewer asks:

**"What is RAG?"**

You can answer:

> **"RAG stands for Retrieval-Augmented Generation. It is a technique where we retrieve relevant information from an external knowledge source, such as a vector database, and provide that information as context to an LLM before generating the answer. The main advantage is that we can use private or updated information without retraining the LLM. A typical RAG pipeline includes document ingestion, chunking, embeddings, vector storage, retrieval, prompt construction, and LLM generation."**

---

### Short Version to Remember

    User Question
         ↓
    Retrieve Relevant Information
         ↓
    Add Context to Prompt
         ↓
    LLM
         ↓
    Generated Answer

**RAG = Retrieval + Context + Generation**

**RAG does NOT retrain the LLM.**