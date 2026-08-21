# What is an LLM?

### Simple Definition

**LLM stands for Large Language Model.**

An LLM is a type of **Generative AI model** trained on a very large amount of text data to **understand and generate human-like language**.

It can perform tasks such as:

- Answering questions
- Generating text
- Writing code
- Summarizing content
- Translating languages
- Extracting information
- Having conversations

### Examples

- GPT
- Gemini
- Claude
- Llama

---

### How Does an LLM Work? — Simple Explanation

At a high level, an LLM works by predicting the **next token** based on the previous tokens and the context.

For example:

> **"React is a JavaScript ___"**

The model predicts a likely next token such as:

> **"library"**

It does this repeatedly:

    Input Prompt
         ↓
    Text is converted into Tokens
         ↓
    Tokens are converted into numerical representations
         ↓
    Transformer processes context
         ↓
    Model predicts the next token
         ↓
    Repeat until the response is complete

For example:

    "What is React?"
         ↓
    [Tokens]
         ↓
    LLM processes the context
         ↓
    Predicts token → "React"
         ↓
    Predicts token → "is"
         ↓
    Predicts token → "a"
         ↓
    Predicts token → "JavaScript"
         ↓
    ...
         ↓
    Final Answer

---

### Why Is It Called "Large"?

The word **Large** usually refers to the scale of the model, such as:

- A large amount of training data
- A large number of parameters
- Large computational requirements

---

### What Are Parameters?

Parameters are the values the model learns during training.

Think of them as the **learned internal settings** that help the model recognize patterns in language.

For example:

    Training Data
         ↓
    Training Process
         ↓
    Model learns patterns
         ↓
    Parameters are adjusted
         ↓
    Trained LLM

---

### The Role of Transformers

Modern LLMs are generally based on the **Transformer architecture**.

One of its important concepts is **attention**, which helps the model determine which parts of the input are most relevant when processing a token.

For example:

> **"The developer deployed the application because it was ready."**

The model uses context to understand what **"it"** refers to.

You don't need to explain the complete mathematics of Transformers for this role, but you should understand:

> **"Transformers use attention mechanisms to process relationships between different parts of the input, which helps LLMs understand context."**

---

### LLM vs Traditional Programming

#### Traditional Programming

    Input + Explicit Rules → Output

Example:

    if (userRole === "admin") {
        return "Access granted";
    }

The developer explicitly writes the rules.

#### LLM

    Input + Learned Patterns → Generated Output

You don't explicitly program every possible answer. The model generates a response based on patterns learned during training and the context provided.

---

### LLM in a Real Application

For an AI-powered interview preparation application:

    React Frontend
         ↓
    Node.js Backend
         ↓
    User sends interview question
         ↓
    LLM API
         ↓
    AI-generated answer / feedback
         ↓
    React displays the response

---

### LLM in a RAG-Based Application

For a **RAG-based application**, the architecture becomes:

    React Frontend
         ↓
    Node.js / Python Backend
         ↓
    User Question
         ↓
    Retrieve relevant documents from Vector DB
         ↓
    Add retrieved context to Prompt
         ↓
    LLM
         ↓
    Generated Answer

This is important because an interviewer may ask:

**"Have you actually worked with LLMs?"**

You can answer:

> **"Yes. I have worked with LLM APIs in my projects. My understanding is that the LLM handles language generation, while the application backend manages prompts, context, APIs, and business logic. In a RAG system, I retrieve relevant document chunks first and provide them as context to the LLM so it can generate a more grounded answer."**

---

### Interview Answer

If the interviewer asks **"What is an LLM?"**, you can say:

> **"LLM stands for Large Language Model. It is a type of Generative AI model trained on a massive amount of text data to understand and generate human-like language. At a high level, an LLM generates text by processing the input as tokens and predicting the next token based on the context. Modern LLMs are generally based on the Transformer architecture and can perform tasks like question answering, summarization, translation, code generation, and conversation."**

---

### Key Takeaways

- **LLM = Large Language Model**
- LLMs are a type of **Generative AI**.
- LLMs are trained on large amounts of data.
- LLMs process text as **tokens**.
- LLMs generate responses through **next-token prediction**.
- Modern LLMs generally use the **Transformer architecture**.
- **Attention** helps the model understand relationships and context.
- Parameters are the learned values inside the model.
- LLMs can be accessed through APIs and integrated into applications.
- RAG provides external context to an LLM to produce more grounded answers.

### Quick Revision

    User Prompt
         ↓
    Tokens
         ↓
    Transformer + Attention
         ↓
    Next Token Prediction
         ↓
    Repeat
         ↓
    Generated Response

### Next Topic

**Tokens — What are tokens and how does tokenization work?**