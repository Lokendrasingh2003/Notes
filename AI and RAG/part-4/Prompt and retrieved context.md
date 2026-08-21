# Prompt + Retrieved Context

## Simple Definition

In a RAG system, **retrieved context** is the relevant information fetched from the knowledge base, and the **prompt** combines that context with the user's question before sending everything to the LLM.

> **Prompt + Retrieved Context = User question + relevant retrieved information given to the LLM**

---

## How It Works

A user asks:

    "How many paid leaves do employees get?"

The system first retrieves relevant information:

    "Employees receive 20 days of paid leave every year."

Then it builds a prompt:

    Context:
    Employees receive 20 days of paid leave every year.

    Question:
    How many paid leaves do employees get?

The complete prompt is sent to the LLM.

    User Question
         ↓
    Retrieve Relevant Chunks
         ↓
    Add Retrieved Context to Prompt
         ↓
    Send Prompt to LLM
         ↓
    Generate Answer

---

## Example Prompt

A RAG prompt may look like this:

    System:
    Answer the user's question using only the provided context.
    If the answer is not available in the context, say that you don't know.

    Context:
    Employees receive 20 days of paid leave every year.

    User Question:
    How many paid leaves do employees get?

The LLM can then generate:

    "Employees receive 20 days of paid leave every year."

---

## Why Add Retrieved Context?

An LLM may not know:

- Private company information
- Internal documents
- Latest information
- Custom knowledge

By adding retrieved context, we provide the LLM with the information needed to answer the question.

> **The retrieved context acts as additional information for the LLM during generation.**

---

## Important Point

The retrieved context is **not used to retrain the LLM**.

It is provided temporarily as part of the input for the current request.

    User Question + Retrieved Context
                 ↓
                LLM
                 ↓
               Answer

For the next request, the system may retrieve different context.

---

## Interview Answer

If the interviewer asks:

**"What do you mean by adding retrieved context to the prompt in RAG?"**

You can answer:

> **"In RAG, after retrieving the most relevant document chunks, we combine those chunks with the user's question in the prompt sent to the LLM. This gives the LLM relevant external information to use while generating the answer. The retrieved context is provided at query time and does not retrain or permanently change the LLM."**

---

## Short Version to Remember

    User Question
         ↓
    Retrieve Relevant Chunks
         ↓
    Prompt = Question + Retrieved Context
         ↓
    LLM
         ↓
    Grounded Answer

**Retrieved Context = Relevant information fetched from external data**

**Prompt = Instructions + Retrieved Context + User Question**