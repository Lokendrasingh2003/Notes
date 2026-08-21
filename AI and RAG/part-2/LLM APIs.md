# LLM APIs

### Simple Definition

An **LLM API** is an interface that allows an application to communicate with a **Large Language Model** and use its capabilities without running the entire model locally.

In simple words:

> **An LLM API allows your application to send a prompt to an LLM and receive a generated response.**

---

### How Does an LLM API Work?

The basic flow is:

    Application
         ↓
    Send Request to LLM API
         ↓
    LLM Processes Request
         ↓
    Generate Response
         ↓
    API Returns Response
         ↓
    Application Displays / Uses Response

For example:

    React Frontend
         ↓
    Node.js Backend
         ↓
    LLM API
         ↓
    LLM
         ↓
    Generated Response
         ↓
    Node.js Backend
         ↓
    React Frontend

---

### What Does an LLM API Request Usually Contain?

An API request can contain:

- Model name
- System instructions
- User prompt
- Conversation history
- Additional context
- Generation parameters
- Other model-specific options

Conceptually:

    API Request
         ↓
    Model
    + System Prompt
    + User Prompt
    + Context
    + Generation Settings

         ↓

    LLM

         ↓

    API Response

---

### Example

Suppose you are building an AI interview application.

The user asks:

    "Explain closures in JavaScript."

Your backend can send the request to an LLM API:

    User Question
         ↓
    Node.js Backend
         ↓
    LLM API
         ↓
    Model
         ↓
    Generated Explanation
         ↓
    Node.js Backend
         ↓
    React UI

The frontend does not need to directly communicate with the LLM provider.

---

### Common LLM API Providers

Examples include:

- OpenAI
- Google Gemini
- Anthropic Claude
- Groq
- OpenRouter

Different providers offer different models, APIs, pricing, speed, context limits, and capabilities.

---

### Why Use an LLM API?

Using an API allows developers to integrate LLM capabilities into applications without having to:

- Train an LLM from scratch
- Host the entire model themselves
- Manage large GPU infrastructure
- Build the complete model architecture

Developers can focus on building the application around the model.

---

### LLM API in a RAG Application

In a RAG application, the LLM API is usually used **after retrieving relevant information**.

The flow is:

    User Question
         ↓
    Generate Query Embedding
         ↓
    Search Vector Database
         ↓
    Retrieve Relevant Chunks
         ↓
    Build Prompt with Context
         ↓
    LLM API
         ↓
    LLM Generates Answer
         ↓
    Return Answer to User

For example:

    User:
    "What is our company's leave policy?"

         ↓

    Retrieve relevant company documents

         ↓

    Context:
    "Employees receive 20 paid leaves per year..."

         ↓

    LLM API

         ↓

    Answer based on the retrieved context

---

### API Key

Most LLM APIs require an **API key** for authentication.

Conceptually:

    Application
         ↓
    API Key + Request
         ↓
    LLM Provider
         ↓
    Response

The API key should be kept **secure**.

For a Node.js application, it should normally be stored in an environment variable rather than hard-coded in the source code.

Example:

    OPENAI_API_KEY=your_api_key

Never expose API keys in frontend code or commit them to GitHub.

---

### Backend vs Frontend

For most applications, the LLM API call should be made from the **backend**.

Recommended architecture:

    React Frontend
         ↓
    Node.js Backend
         ↓
    LLM API
         ↓
    LLM

Instead of:

    React Frontend
         ↓
    LLM API

The backend approach helps protect API keys and allows you to control:

- Authentication
- Authorization
- Prompt construction
- Business logic
- Rate limiting
- Logging
- Validation
- RAG retrieval

---

### LLM API vs LLM

These are not the same thing.

**LLM:**

> The actual language model that processes input and generates output.

**LLM API:**

> The interface that allows an application to communicate with and use the model.

Conceptually:

    Application
         ↓
    LLM API
         ↓
    LLM
         ↓
    Response

---

### Interview Answer

If the interviewer asks:

**"What is an LLM API?"**

You can answer:

> **"An LLM API is an interface that allows an application to communicate with a large language model. The application sends a request containing things like the model, prompts, and context, and the API returns the model's generated response. In applications such as RAG systems, the backend can retrieve relevant documents first, add that information to the prompt, and then send it to the LLM API to generate a grounded response."**

---

### Short Version to Remember

    Application
         ↓
    Backend
         ↓
    LLM API
         ↓
    LLM
         ↓
    Generated Response

**LLM = Model**

**LLM API = Interface used to communicate with the model**