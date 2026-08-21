# How Does an LLM Work?

For your interview, you **do not need to explain the complex mathematics**. You should understand the complete high-level flow.

---

### High-Level Flow

#### Training Phase

    Large Text Data
         ↓
    Tokenization
         ↓
    Tokens → Numbers / Vectors
         ↓
    Transformer Neural Network
         ↓
    Learn language patterns by predicting the next token
         ↓
    Trained LLM

#### Inference Phase

    User Prompt
         ↓
    Tokenization
         ↓
    Transformer processes context
         ↓
    Predict next token
         ↓
    Predict next token again
         ↓
    ...
         ↓
    Final Response

---

### 1. Training on Large Amounts of Data

An LLM is trained on a very large amount of **text and code**.

For example, during training, the model sees patterns like:

> "React is a JavaScript library for building user interfaces."

The model is not simply storing this sentence as a fixed answer. During training, it learns **patterns and relationships in language**.

Its training objective is often:

> **Given the previous tokens, predict the next token.**

Example:

    Input:
    "React is a JavaScript"

    Target:
    "library"

The model makes a prediction, compares it with the correct token, calculates the error, and updates its internal parameters.

This process is repeated over a huge amount of data.

---

### 2. Tokenization

Before processing text, the LLM converts it into **tokens**.

A token can be:

- A whole word
- Part of a word
- A punctuation mark
- Another piece of text

For example:

    "Hello, how are you?"

         ↓

    ["Hello", ",", " how", " are", " you", "?"]

The exact tokens depend on the tokenizer.

LLMs don't directly process normal text. They process numerical representations of tokens.

    Text
      ↓
    Tokens
      ↓
    Token IDs / Numerical Representations

---

### 3. Embeddings / Numerical Representations

The tokens are converted into numerical representations that the neural network can process.

Conceptually:

    "React"
       ↓
    [0.12, -0.45, 0.87, ...]

These numerical representations help the model process relationships and patterns.

> **Important:** Token embeddings used inside an LLM are not exactly the same thing as the embeddings commonly used for semantic search in a RAG vector database.

Both represent information numerically, but they can serve different purposes.

---

### 4. Transformer Processes the Context

The numerical representations are passed through the **Transformer architecture**.

A key concept here is **attention**.

Attention helps the model determine which parts of the input are most relevant to each other.

For example:

> "The developer deployed the application because **it** was ready."

The model uses the surrounding context to understand what **"it"** refers to.

You can say in an interview:

> **"The Transformer uses attention mechanisms to understand relationships and context between different tokens in the input."**

---

### 5. The Model Predicts the Next Token

This is the core high-level idea.

Suppose the input is:

    "Node.js is used for"

The LLM calculates probabilities for possible next tokens:

    "building"   → 45%
    "backend"    → 30%
    "creating"   → 10%
    "database"   → 2%
    ...

It selects a token according to its generation strategy.

For example:

    Node.js is used for
         ↓
      "building"

Now the generated token becomes part of the context:

    Node.js is used for building

Then it predicts again:

    Node.js is used for building
         ↓
      "server-side"

This continues until the response is complete.

    Prompt
      ↓
    Predict token 1
      ↓
    Add token 1 to context
      ↓
    Predict token 2
      ↓
    Add token 2 to context
      ↓
    Repeat
      ↓
    Final Response

---

### Training vs Inference

This is a **very important difference**.

| Training | Inference |
|---|---|
| Model learns from data | Model generates an answer |
| Parameters are updated | Parameters usually remain fixed |
| Very computationally expensive | Less expensive than training |
| Happens before deployment | Happens when users send prompts |

#### Training

    Huge Dataset
         ↓
    Predict next token
         ↓
    Calculate error
         ↓
    Update parameters
         ↓
    Repeat
         ↓
    Trained Model

#### Inference

    User Prompt
         ↓
    Trained LLM
         ↓
    Generate tokens one by one
         ↓
    Response

---

### How This Connects to RAG

An LLM does **not automatically know your company's private or latest documents**.

So with RAG:

    User Question
         ↓
    Convert question into an embedding
         ↓
    Search Vector Database
         ↓
    Retrieve relevant document chunks
         ↓
    Add chunks to the prompt as context
         ↓
    LLM processes Prompt + Context
         ↓
    Generates grounded answer

The important point is:

> **RAG does not retrain the LLM. It retrieves relevant external information at query time and provides it as context to the LLM.**

---

### Interview Answer

> **"At a high level, an LLM is trained on a large amount of text to learn language patterns. The input text is first broken into tokens and converted into numerical representations. These are processed by a Transformer architecture, which uses attention mechanisms to understand the context and relationships between tokens. During generation, the model predicts the next token based on the previous context, adds that token to the sequence, and repeats the process until it produces the complete response."**

### Short Version

    Input
      ↓
    Tokenization
      ↓
    Numerical Representations
      ↓
    Transformer + Attention
      ↓
    Next-Token Prediction
      ↓
    Repeat
      ↓
    Final Response