# Context Window

### Simple Definition

A **context window** is the maximum amount of information, measured in **tokens**, that an LLM can process and consider at one time.

In simple words:

> **The context window is the amount of text and conversation an LLM can "see" and use while generating a response.**

---

### How Does It Work?

When you send a prompt to an LLM, the model receives the relevant context as tokens.

For example:

    System Instructions
           +
    Previous Conversation
           +
    User Prompt
           +
    RAG Context
           ↓
    Context Window
           ↓
    LLM
           ↓
    Generated Response

All of this information must fit within the model's supported context window.

---

### Example

Suppose an LLM has a context window of:

    100,000 tokens

And the request contains:

    System instructions → 2,000 tokens
    Conversation        → 10,000 tokens
    User prompt         → 1,000 tokens
    RAG documents       → 20,000 tokens

Total:

    2,000 + 10,000 + 1,000 + 20,000
    = 33,000 tokens

Since 33,000 tokens is within the 100,000-token limit, the model can process the request.

---

### Context Window Includes More Than the User Prompt

The context window can contain:

- System instructions
- User prompts
- Previous conversation
- Tool results
- Retrieved RAG documents
- Other information provided to the model
- Generated output, depending on the model/API context rules

Therefore, the context window is not simply the size of the user's message.

---

### Context Window vs Memory

A **context window is not the same as permanent memory**.

#### Context Window

The model can use information that is currently included in its context.

    Current Context
         ↓
    LLM can use it

#### Long-Term Memory

Information can be stored externally and retrieved when needed.

    Stored Information
         ↓
    Retrieve Relevant Information
         ↓
    Add to Context
         ↓
    LLM

This is why applications can use databases, vector databases, or memory systems to provide information to an LLM when needed.

---

### Context Window in RAG

Context windows are especially important in **RAG systems**.

Suppose you have a large collection of documents.

You generally don't send every document to the LLM.

Instead:

    User Question
         ↓
    Search / Retrieval
         ↓
    Relevant Document Chunks
         ↓
    Add Relevant Chunks to Prompt
         ↓
    LLM Context Window
         ↓
    Generated Answer

This reduces unnecessary information and helps keep the request within the context limit.

---

### Why Is Context Window Important?

A larger context window can allow an LLM to process more information in a single request.

It is useful for:

- Long conversations
- Large documents
- Codebases
- Multiple documents
- RAG applications
- Complex instructions
- Long-form analysis

However:

> **A larger context window does not automatically mean the model will use every piece of information equally well.**

The quality of the retrieved and relevant context still matters.

---

### Context Window and Token Limits

Context windows are measured in **tokens**, not words.

For example:

    Context Window = 100,000 tokens

The actual number of words that fit depends on the language and tokenizer because:

    1 word ≠ always 1 token

The model's input and output limits also depend on the specific model and API.

---

### What Happens If the Context Is Too Large?

If the amount of information exceeds the model's supported context window, the application may need to:

- Remove older conversation history
- Summarize previous messages
- Reduce retrieved documents
- Reduce chunk size
- Retrieve only the most relevant chunks
- Use a model with a larger context window

In RAG, this is one reason **retrieval and chunking** are important.

---

### Context Window vs Token Limit

These terms are related but should not always be treated as exactly the same thing.

**Context window:**

> The total amount of token context the model can handle for a request.

**Output token limit:**

> The maximum number of tokens the model can generate for the response.

Conceptually:

    Context Window
    ┌──────────────────────────────┐
    │ Input + Context + Output    │
    └──────────────────────────────┘

The exact accounting depends on the model and API.

---

### Interview Answer

If the interviewer asks:

**"What is a context window in an LLM?"**

You can answer:

> **"A context window is the maximum amount of information, measured in tokens, that an LLM can process within a request. It can include the system instructions, user prompt, conversation history, and retrieved context such as documents in a RAG system. A larger context window allows the model to handle more information at once, but the relevant context still needs to be selected carefully."**

---

### Short Version to Remember

    Context Window
         ↓
    Maximum tokens the model can process
         ↓
    Prompt + Conversation + Context
         ↓
    LLM
         ↓
    Response

### Key Takeaways

- **Context window = maximum amount of context an LLM can process**
- It is measured in **tokens**.
- It can include prompts, conversation history, instructions, and RAG context.
- It is **not the same as permanent memory**.
- RAG retrieves relevant information and places it into the context.
- A larger context window allows more information to be processed.
- Context management is important for **cost, performance, and response quality**.