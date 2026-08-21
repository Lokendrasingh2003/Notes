# Function / Tool Calling

### Simple Definition

**Function calling** or **tool calling** allows an LLM to request that an application execute a specific function or external tool.

The LLM itself does not directly execute the function. Instead, it decides **which tool to use and what arguments to provide**, and the application executes it.

In simple words:

> **Tool calling allows an LLM to interact with external systems and perform actions beyond generating text.**

---

### Why Is Tool Calling Needed?

An LLM by itself mainly generates text.

It may not have direct access to:

- Databases
- APIs
- Real-time information
- Application functions
- File systems
- External services

Tool calling allows the LLM to interact with these systems.

For example:

    User:
    "What is the weather in Delhi?"

    ↓

    LLM decides:
    "I need to use the weather tool."

    ↓

    Weather Tool

    ↓

    Weather Data

    ↓

    LLM

    ↓

    Final Answer

---

### How Does Function / Tool Calling Work?

The high-level flow is:

    User Request
         ↓
    LLM
         ↓
    Decide whether a tool is needed
         ↓
    Select Tool
         ↓
    Generate Tool Arguments
         ↓
    Application Executes Tool
         ↓
    Tool Result
         ↓
    Send Result Back to LLM
         ↓
    LLM Generates Final Response

---

### Example

Suppose an application has this function:

    getWeather(city)

The user asks:

    "What is the weather in Delhi?"

The LLM can determine that it needs the weather function.

It may generate a tool call conceptually like:

    Tool:
    getWeather

    Arguments:
    {
        "city": "Delhi"
    }

The application then executes:

    getWeather("Delhi")

The tool returns:

    {
        "temperature": 32,
        "condition": "Sunny"
    }

The result is sent back to the LLM.

The LLM can then respond:

    "The weather in Delhi is currently 32°C and sunny."

---

### Important Point

The LLM does **not necessarily execute the function itself**.

Instead:

    LLM
     ↓
    Requests Tool Call
     ↓
    Application
     ↓
    Executes Function
     ↓
    Returns Result
     ↓
    LLM
     ↓
    Final Response

This distinction is important in interviews.

---

### Function Calling vs Normal Text Generation

#### Normal LLM Response

    User
     ↓
    LLM
     ↓
    Text Response

Example:

    User:
    "What is JavaScript?"

    LLM:
    "JavaScript is a programming language..."

---

#### Tool Calling

    User
     ↓
    LLM
     ↓
    Tool Call
     ↓
    Application Executes Tool
     ↓
    Tool Result
     ↓
    LLM
     ↓
    Final Response

Example:

    User:
    "What is my account balance?"

The LLM can request:

    getAccountBalance(userId)

The application executes the function and returns the actual balance.

---

### Function Calling in a Real Application

Imagine an e-commerce application.

Available tools:

    getProductDetails(productId)

    checkInventory(productId)

    createOrder(productId, quantity)

A user asks:

    "Do you have the iPhone 16 in stock?"

The flow could be:

    User Question
         ↓
    LLM
         ↓
    checkInventory()
         ↓
    Database
         ↓
    Inventory Result
         ↓
    LLM
         ↓
    Answer

If the user then says:

    "Order one for me."

The LLM could request:

    createOrder(productId, quantity)

The backend executes the function and returns the result.

---

### Tool Calling in AI Agents

Tool calling is an important part of **AI agents**.

An agent can:

- Understand the user's goal
- Decide which tool is needed
- Call the tool
- Observe the result
- Decide what to do next
- Call another tool if necessary
- Return the final answer

For example:

    User Request
         ↓
    AI Agent
         ↓
    Search Tool
         ↓
    Search Result
         ↓
    Database Tool
         ↓
    Database Result
         ↓
    LLM
         ↓
    Final Answer

---

### Tool Calling in RAG

Tool calling can also be used in RAG applications.

For example, the LLM can use a retrieval tool:

    User Question
         ↓
    LLM
         ↓
    retrieveDocuments(query)
         ↓
    Vector Database
         ↓
    Relevant Documents
         ↓
    LLM
         ↓
    Answer

The retrieval function acts as a tool that gives the LLM access to external knowledge.

---

### Function Calling vs API Calling

A tool can internally call an API.

For example:

    LLM
     ↓
    getWeather(city)
     ↓
    Weather API
     ↓
    Weather Data
     ↓
    LLM
     ↓
    Response

The LLM interacts with the application's **tool/function**, while the function can communicate with an external API.

---

### Interview Answer

If the interviewer asks:

**"What is function calling or tool calling in LLMs?"**

You can answer:

> **"Function calling allows an LLM to request the execution of a predefined function or external tool when it needs information or wants to perform an action. The model decides which tool to call and provides the required arguments, while the application executes the function and sends the result back to the model. This allows LLM applications and agents to interact with databases, APIs, and other external systems."**

---

### Short Version to Remember

    User Request
         ↓
    LLM
         ↓
    Select Tool + Arguments
         ↓
    Application Executes Tool
         ↓
    Tool Result
         ↓
    LLM
         ↓
    Final Response

**LLM decides what tool to use.**

**Application executes the tool.**

**Tool returns the result.**