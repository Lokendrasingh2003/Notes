# Chunking Strategies

## Simple Definition

**Chunking** is the process of splitting a large document into smaller pieces called **chunks** before generating embeddings and storing them in a vector database.

In RAG:

    Large Document
         ↓
      Chunking
         ↓
    Smaller Chunks
         ↓
    Generate Embeddings
         ↓
    Vector Database

> **Good chunking helps the system retrieve more relevant information.**

---

## Why Is Chunking Needed?

Large documents are usually too big to treat as one piece.

For example:

    100-page PDF
         ↓
    Cannot efficiently retrieve the exact relevant information
         ↓
    Split into smaller chunks
         ↓
    Search only the relevant chunks

If the chunks are too large, retrieval may include too much irrelevant information.

If the chunks are too small, important context may be lost.

> **The goal is to choose a chunk size that keeps enough context while still being specific enough for accurate retrieval.**

---

# Common Chunking Strategies

## 1. Fixed-Size Chunking

The document is split into chunks of a fixed number of characters or tokens.

Example:

    Document
         ↓
    Every 500 tokens
         ↓
    Chunk 1
    Chunk 2
    Chunk 3

### Advantages

- Simple to implement
- Fast
- Easy to control chunk size

### Disadvantages

- May split a sentence or idea in the middle
- Can lose semantic meaning

---

## 2. Chunking with Overlap

Adjacent chunks share some common text.

Example:

    Chunk 1:
    [------------------------]

    Chunk 2:
              [------------------------]

          ← Overlap →

The overlap helps preserve context when important information appears near the boundary between two chunks.

Example:

    Chunk 1:
    "React is a JavaScript library used for..."

    Chunk 2:
    "...used for building user interfaces."

The repeated content helps maintain continuity.

### Advantages

- Preserves context
- Reduces information loss at chunk boundaries

### Disadvantages

- Stores more data
- Creates more embeddings
- Increases storage and processing cost

---

## 3. Recursive Chunking

Recursive chunking tries to split text using natural separators.

For example:

    Document
         ↓
    Chapter
         ↓
    Section
         ↓
    Paragraph
         ↓
    Sentence

The system tries larger separators first and moves to smaller separators when necessary.

> **This usually preserves the natural structure of the text better than blindly splitting at a fixed position.**

---

## 4. Semantic Chunking

Semantic chunking splits the document based on **meaning** rather than only a fixed size.

For example:

    Topic: React Components
         ↓
    Chunk 1

    Topic: React Hooks
         ↓
    Chunk 2

    Topic: State Management
         ↓
    Chunk 3

The goal is to keep related information together.

### Advantages

- Better semantic coherence
- Can improve retrieval quality

### Disadvantages

- More complex
- Usually requires additional processing

---

## 5. Document Structure-Based Chunking

This strategy uses the structure of the document.

For example:

    PDF
      ↓
    Chapters
      ↓
    Sections
      ↓
    Subsections

For technical documentation:

    Heading
      ↓
    Related Content
      ↓
    One Chunk

Example:

    ## Authentication

    Related authentication content
         ↓
    Chunk

This is often useful when documents have clear headings and sections.

---

## Chunk Size

Chunk size determines how much content each chunk contains.

For example:

    Small Chunk
    ├── More specific retrieval
    └── Less context

    Large Chunk
    ├── More context
    └── May include irrelevant information

There is no single perfect chunk size.

The best size depends on:

- Type of documents
- Type of questions
- Embedding model
- LLM context window
- Required retrieval accuracy

A common starting point is to experiment with a moderate chunk size and evaluate retrieval quality.

---

## Chunk Overlap

Chunk overlap means repeating some content between adjacent chunks.

Example:

    Chunk 1:
    [A B C D E F]

    Chunk 2:
            [E F G H I J]

Here:

    E F

is the overlap.

Overlap can help preserve context across chunk boundaries.

---

## Example in RAG

Suppose we have a long company policy document:

    Company Policy PDF
            ↓
    Extract Text
            ↓
    Recursive Chunking
            ↓
    500-token Chunks
    + Some Overlap
            ↓
    Generate Embeddings
            ↓
    Store in Vector Database

Later:

    User Question
            ↓
    Generate Query Embedding
            ↓
    Similarity Search
            ↓
    Retrieve Relevant Chunks
            ↓
    LLM
            ↓
    Answer

---

## Which Strategy Should You Use?

| Strategy | Best For |
|---|---|
| Fixed-size | Simple documents and quick prototypes |
| Overlapping chunks | Preserving context |
| Recursive chunking | General-purpose RAG |
| Semantic chunking | Meaning-based retrieval |
| Structure-based | Well-organized documents |

For many RAG applications, a good starting approach is:

> **Recursive chunking with a reasonable chunk size and some overlap.**

Then evaluate the retrieval quality and adjust the strategy if necessary.

---

## Interview Answer

If the interviewer asks:

**"What is chunking, and what chunking strategies do you know?"**

You can answer:

> **"Chunking is the process of splitting large documents into smaller pieces before generating embeddings and storing them in a vector database. Common strategies include fixed-size chunking, overlapping chunking, recursive chunking, semantic chunking, and document structure-based chunking. The choice of chunk size and overlap is important because chunks that are too large may contain irrelevant information, while chunks that are too small can lose important context. For a general RAG application, recursive chunking with some overlap is a common starting point."**

---

## Short Version to Remember

    Large Document
         ↓
      Chunking
         ↓
    Smaller Chunks
         ↓
     Embeddings
         ↓
    Vector Database

**Common strategies:**

- Fixed-size
- Overlapping
- Recursive
- Semantic
- Structure-based

> **Good chunking = Better retrieval + Enough context**