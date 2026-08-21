# RAG Evaluation

### Simple Definition

**RAG evaluation** is the process of measuring how well a RAG system **retrieves relevant information and generates accurate, grounded answers**.

In simple words:

> **RAG Evaluation = Check whether the system retrieves the right context and gives the right answer.**

---

## What Should We Evaluate?

A RAG system has two main parts:

    Retrieval
         ↓
    Generation

So we evaluate both.

### 1. Retrieval Quality

Did the system retrieve the **right documents/chunks**?

### 2. Generation Quality

Did the LLM generate a **correct and grounded answer** using the retrieved context?

---

# 1. Retrieval Evaluation

Retrieval evaluation checks whether the retriever finds the relevant information.

Important metrics include:

- **Precision**
- **Recall**
- **Context Relevance**

---

## Precision

**Precision** measures how many of the retrieved results are actually relevant.

Example:

    Retrieved 5 chunks

    Relevant chunks = 4

    Precision = 4 / 5 = 80%

High precision means:

> The system is retrieving mostly relevant information.

---

## Recall

**Recall** measures how much of the relevant information was successfully retrieved.

Example:

Suppose there are:

    5 relevant chunks in the database

The system retrieves:

    4 of them

Then:

    Recall = 4 / 5 = 80%

High recall means:

> The system is finding most of the relevant information.

---

## Context Relevance

Context relevance checks whether the retrieved context is actually relevant to the user's question.

Example:

    Question:
    "What is the company's leave policy?"

Retrieved:

    "Employees receive 20 days of paid leave."

    → Relevant

Retrieved:

    "Employees can work remotely two days per week."

    → Not relevant

A good RAG system should retrieve context that is relevant to the query.

---

# 2. Generation Evaluation

After retrieval, we need to evaluate the LLM's answer.

Important concepts include:

- **Answer Relevance**
- **Faithfulness**
- **Groundedness**
- **Correctness**

---

## Answer Relevance

Checks whether the generated answer actually answers the user's question.

Example:

    Question:
    "What is RAG?"

    Answer:
    "RAG stands for Retrieval-Augmented Generation..."

    → Relevant

If the answer starts explaining unrelated information:

    → Low relevance

---

## Faithfulness / Groundedness

Checks whether the answer is supported by the retrieved context.

Example:

    Retrieved Context:
    "Employees receive 20 days of paid leave."

    Generated Answer:
    "Employees receive 20 days of paid leave."

    → Grounded

But:

    Generated Answer:
    "Employees receive 30 days of paid leave."

    → Not grounded

The answer contains information that is not supported by the retrieved context.

---

## Answer Correctness

Checks whether the final answer is actually correct compared with the expected answer or ground truth.

Example:

    Expected:
    "Employees receive 20 days of paid leave."

    Generated:
    "Employees receive 20 days of paid leave."

    → Correct

---

# RAG Evaluation Flow

A typical evaluation process looks like:

    User Question
         ↓
    RAG System
         ↓
    Retrieved Context
         ↓
    Generated Answer
         ↓
    Evaluation
         ↓
    ┌─────────────────────┐
    │ Retrieval Quality   │
    │ Answer Relevance    │
    │ Faithfulness        │
    │ Correctness         │
    └─────────────────────┘

---

# Example Evaluation Dataset

You can create a dataset containing:

| Question | Expected Answer | Relevant Context |
|---|---|---|
| What is RAG? | Retrieval-Augmented Generation | RAG definition |
| What is ChromaDB? | Vector database | ChromaDB documentation |
| What is the leave policy? | 20 days | Leave policy document |

Then run these questions through your RAG system and compare the results.

---

# Common RAG Evaluation Metrics

| Metric | What it Measures |
|---|---|
| Precision | How many retrieved results are relevant |
| Recall | How many relevant results were retrieved |
| Context Relevance | Whether retrieved context is relevant |
| Answer Relevance | Whether the answer addresses the question |
| Faithfulness | Whether answer is supported by context |
| Groundedness | Whether answer is based on retrieved information |
| Answer Correctness | Whether the final answer is correct |

---

# Improving RAG Using Evaluation

Evaluation helps identify where the problem is.

### Problem: Low Retrieval Quality

Possible improvements:

- Better chunking
- Better embeddings
- Hybrid search
- Query transformation
- Reranking
- Metadata filtering

### Problem: Good Retrieval but Wrong Answer

Possible improvements:

- Better prompt
- Better LLM
- Better context formatting
- Stronger grounding instructions

---

# Important Point

A RAG system can fail in two different ways:

### Retrieval Failure

    User Question
         ↓
    Wrong Context
         ↓
    LLM
         ↓
    Wrong Answer

### Generation Failure

    User Question
         ↓
    Correct Context
         ↓
    LLM
         ↓
    Wrong / Unsupported Answer

Therefore:

> **RAG evaluation should measure both retrieval quality and generation quality.**

---

# Interview Answer

If the interviewer asks:

**"How do you evaluate a RAG system?"**

You can answer:

> **"I would evaluate both the retrieval and generation stages. For retrieval, I would measure metrics such as precision, recall, and context relevance to check whether the correct chunks are being retrieved. For generation, I would evaluate answer relevance, faithfulness or groundedness, and answer correctness to check whether the response is accurate and supported by the retrieved context. I would use a representative evaluation dataset containing questions, expected answers, and relevant context."**

---

# Short Version to Remember

    RAG Evaluation
         ↓
    ┌──────────────────┐
    │ Retrieval        │
    │ Precision        │
    │ Recall           │
    │ Context Relevance│
    └────────┬─────────┘
             ↓
    ┌──────────────────┐
    │ Generation       │
    │ Answer Relevance │
    │ Faithfulness     │
    │ Groundedness     │
    │ Correctness      │
    └──────────────────┘

**Good RAG = Relevant Retrieval + Grounded + Correct Answer**