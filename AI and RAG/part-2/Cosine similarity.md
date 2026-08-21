# Cosine Similarity

### Simple Definition

**Cosine similarity** is a method used to measure how similar two vectors are by calculating the **cosine of the angle between them**.

In AI and RAG, it is commonly used to measure the semantic similarity between **embeddings**.

In simple words:

> **Cosine similarity tells us how similar two vectors are based on their direction.**

---

### Why Is Cosine Similarity Used?

When text is converted into embeddings, it becomes a vector.

For example:

    "I love programming"
            ↓
    [0.2, 0.5, 0.8, ...]

    "I enjoy coding"
            ↓
    [0.3, 0.6, 0.7, ...]

We can use cosine similarity to determine how similar these vectors are.

---

### Basic Idea

Imagine two vectors:

    Vector A
       ↗
      /
     /
    ●────────→ Vector B

If the vectors point in a similar direction:

> **High cosine similarity**

If they point in very different directions:

> **Low cosine similarity**


::contentReference[oaicite:0]{index=0}


---

### Cosine Similarity Formula

The formula is:

    cosine similarity = (A · B) / (||A|| × ||B||)

Where:

- **A · B** = Dot product of vectors A and B
- **||A||** = Magnitude of vector A
- **||B||** = Magnitude of vector B

The result is commonly between:

    -1 → Opposite directions
     0 → No directional similarity
    +1 → Same direction

For many text embedding use cases:

> **Closer to 1 = More semantically similar**

---

### Simple Example

Suppose:

    A = [1, 0]

    B = [1, 0]

They point in exactly the same direction.

Therefore:

    Cosine Similarity = 1

Now:

    A = [1, 0]

    B = [0, 1]

They are perpendicular.

Therefore:

    Cosine Similarity = 0

---

### Cosine Similarity in RAG

Cosine similarity is commonly used during **semantic search**.

The process is:

    Documents
         ↓
    Generate Embeddings
         ↓
    Store Vectors
         ↓
    Vector Database

Then:

    User Question
         ↓
    Generate Query Embedding
         ↓
    Compare Query Vector
    with Document Vectors
         ↓
    Calculate Cosine Similarity
         ↓
    Rank Similar Documents
         ↓
    Retrieve Top-K Chunks
         ↓
    LLM
         ↓
    Answer

---

### Example in RAG

Suppose the user asks:

    "How do I create a React component?"

The query is converted into an embedding.

The vector database may contain:

    Document A:
    "React components can be created using functions."

    Document B:
    "MongoDB is a NoSQL database."

The similarity scores might conceptually be:

    Query ↔ Document A → 0.91
    Query ↔ Document B → 0.18

Since Document A has a higher similarity score, it is more relevant to the query.

---

### Cosine Similarity vs Keyword Search

#### Keyword Search

Looks mainly for matching words.

    Query:
    "How to create React components?"

    Document:
    "React components are created using functions."

There may be useful information even when exact words don't match.

#### Semantic Search

Uses embeddings and similarity.

    Query
      ↓
    Embedding
      ↓
    Compare with document embeddings
      ↓
    Find similar meaning

This allows the system to find relevant information even when the exact words are different.

---

### Cosine Similarity in a Vector Database

A vector database can use similarity measures to find the vectors closest to a query vector.

Conceptually:

    Query Vector
         ↓
    Compare with stored vectors
         ↓
    Similarity Scores
         ↓
    Sort by similarity
         ↓
    Top-K Results

For example:

    Document A → 0.92
    Document B → 0.84
    Document C → 0.41
    Document D → 0.15

Top-K = 2

Result:

    Document A
    Document B

These documents can then be provided to the LLM as context.

---

### Important Point

Cosine similarity focuses on the **angle/direction between vectors**, not simply their magnitude.

Therefore, two vectors can have different lengths but still have high cosine similarity if they point in similar directions.

---

### Interview Answer

If the interviewer asks:

**"What is cosine similarity and how is it used in RAG?"**

You can answer:

> **"Cosine similarity is a metric used to measure the similarity between two vectors by calculating the cosine of the angle between them. In RAG, documents and user queries are converted into embeddings, and cosine similarity can be used to compare the query embedding with document embeddings. The documents with the highest similarity scores are considered more relevant and can be retrieved as context for the LLM."**

---

### Short Version to Remember

    Text
      ↓
    Embeddings
      ↓
    Vectors
      ↓
    Cosine Similarity
      ↓
    Measure Semantic Similarity
      ↓
    Retrieve Most Relevant Documents

**Cosine similarity = Measures similarity based on the direction of vectors.**

**Higher score → More similar**

**Lower score → Less similar**