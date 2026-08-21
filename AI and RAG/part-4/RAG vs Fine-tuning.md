# RAG vs Fine-Tuning

## Simple Definition

**RAG** and **Fine-Tuning** are two different ways to improve how an LLM works for a specific use case.

> **RAG gives the LLM external information at query time.**

> **Fine-tuning changes the model's behavior by training it further on specific data.**

---

## RAG

**RAG (Retrieval-Augmented Generation)** retrieves relevant information and adds it to the prompt before the LLM generates an answer.

    User Question
         ↓
    Retrieve Relevant Documents
         ↓
    Add Context to Prompt
         ↓
    LLM
         ↓
    Answer

### Example

Suppose a company has internal documents about its leave policy.

User asks:

    "How many paid leaves do employees get?"

RAG:

    User Question
         ↓
    Search Company Documents
         ↓
    Retrieve Relevant Leave Policy
         ↓
    Add Context to Prompt
         ↓
    LLM
         ↓
    Answer

The LLM's parameters are **not changed**.

---

## Fine-Tuning

**Fine-tuning** means training an existing pre-trained model further on a specific dataset.

The purpose is usually to improve the model's behavior, style, format, or performance for a particular task.

    Pre-trained Model
         ↓
    Specific Training Data
         ↓
    Fine-Tuning
         ↓
    Updated Model

### Example

Suppose you want a model that always generates customer-support responses in a specific format and style.

You can provide many examples:

    User Question → Desired Response

         ↓

    Fine-Tuning

         ↓

    Model learns the desired behavior

---

## Key Difference

| RAG | Fine-Tuning |
|---|---|
| Retrieves external information | Trains the model further |
| Model parameters are not changed | Model parameters are updated |
| Information is added at query time | Knowledge/behavior is learned during training |
| Good for private or changing data | Good for specific behavior or tasks |
| Easy to update documents | Requires another training process to update the model |
| Uses a retriever/vector database | Uses a training dataset |

---

## When to Use RAG?

Use RAG when you need:

- Private company documents
- Frequently changing information
- Latest information
- Knowledge bases
- Document question-answering

Example:

> A chatbot that answers questions using company policies and internal documents.

---

## When to Use Fine-Tuning?

Use fine-tuning when you need:

- Specific response style
- Consistent output format
- Better performance on a specialized task
- Domain-specific behavior

Example:

> Training a model to always generate structured customer-support responses.

---

## Can RAG and Fine-Tuning Be Used Together?

Yes.

A system can use both:

    User Question
         ↓
    RAG retrieves current information
         ↓
    Fine-Tuned LLM
         ↓
    Answer

For example:

- **RAG** provides current company information.
- **Fine-tuning** helps the model follow a specific response style or behavior.

---

## Important Point

A simple way to remember the difference:

> **RAG changes the context.**

> **Fine-tuning changes the model.**

RAG is usually better for adding **new or changing knowledge**, while fine-tuning is useful for improving **how the model behaves or performs a task**.

---

## Interview Answer

If the interviewer asks:

**"What is the difference between RAG and fine-tuning?"**

You can answer:

> **"RAG and fine-tuning solve different problems. RAG retrieves relevant external information at query time and provides it as context to the LLM, without changing the model's parameters. Fine-tuning trains an existing model further on a specific dataset, which updates the model's parameters and can improve its behavior or performance for a particular task. I would typically use RAG for private or frequently changing knowledge and fine-tuning for specialized behavior or tasks."**

---

## Short Version to Remember

    RAG:
    External Information
           ↓
    Retrieve at Query Time
           ↓
    Add to Prompt
           ↓
    LLM

    Fine-Tuning:
    Training Data
           ↓
    Train Model Further
           ↓
    Updated Model

**RAG = Changes the context**

**Fine-Tuning = Changes the model**