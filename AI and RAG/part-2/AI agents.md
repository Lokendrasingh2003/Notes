# AI Agents

### Simple Definition

An **AI Agent** is an AI system that can **understand a goal, make decisions, use tools, and take actions** to accomplish a task.

In simple words:

> **An AI agent is an LLM-based system that can reason about a task, decide what to do, use available tools, observe the results, and continue until it reaches the goal.**

---

### LLM vs AI Agent

An **LLM** mainly generates responses based on the given input and context.

    User Prompt
         ↓
    LLM
         ↓
    Response

An **AI Agent** can take multiple steps and interact with external tools.

    User Goal
         ↓
    AI Agent
         ↓
    Decide What to Do
         ↓
    Use Tool
         ↓
    Observe Result
         ↓
    Decide Next Step
         ↓
    Use Another Tool
         ↓
    Final Response

---

### Basic Components of an AI Agent

An AI agent commonly consists of:

- **LLM** — reasoning and decision-making
- **Tools** — allow the agent to interact with external systems
- **Instructions** — define the agent's behavior and goals
- **Memory / State** — stores relevant information during the task
- **Environment** — the external system the agent interacts with

Conceptually:

    User Goal
         ↓
    AI Agent
    ┌───────────────┐
    │      LLM      │
    │               │
    │  Instructions │
    │  Memory/State │
    │     Tools     │
    └───────────────┘
         ↓
    External Systems
         ↓
    Results
         ↓
    AI Agent
         ↓
    Final Response

---

### How Does an AI Agent Work?

A basic agent loop looks like this:

    User Goal
         ↓
    Understand the Task
         ↓
    Decide Next Action
         ↓
    Select Tool
         ↓
    Execute Tool
         ↓
    Observe Result
         ↓
    Decide Next Action
         ↓
    Repeat if Necessary
         ↓
    Final Answer

This is often called an **agent loop**.

---

### Example

Suppose the user asks:

> "Find the weather in Delhi and tell me whether I should carry an umbrella."

The agent can:

    User Request
         ↓
    AI Agent
         ↓
    Call Weather Tool
         ↓
    Get Weather Data
         ↓
    Analyze Result
         ↓
    Generate Recommendation
         ↓
    Final Answer

The agent is not simply generating a response from its existing knowledge. It is **using a tool to obtain information and then acting on the result**.

---

### AI Agents and Tool Calling

**Tool calling is an important capability of AI agents.**

For example, an agent may have access to:

    searchWeb()
    getWeather()
    queryDatabase()
    sendEmail()
    createOrder()

The agent decides which tool is appropriate for the current task.

    User Goal
         ↓
    LLM / Agent
         ↓
    Select Tool
         ↓
    Execute Tool
         ↓
    Tool Result
         ↓
    Agent
         ↓
    Next Action or Final Answer

---

### AI Agent Example in a Real Application

Imagine an AI customer-support agent.

The user asks:

> "Where is my order?"

The agent can:

    User Question
         ↓
    AI Agent
         ↓
    queryOrderDatabase()
         ↓
    Order Status
         ↓
    Agent analyzes result
         ↓
    Final Response

If the user then asks:

> "Can you cancel it?"

The agent may use:

    cancelOrder(orderId)

The backend executes the action and returns the result.

---

### AI Agents vs RAG

**RAG** is mainly used to provide an LLM with relevant external information.

    Question
       ↓
    Retrieve Documents
       ↓
    Add Context
       ↓
    LLM
       ↓
    Answer

An **AI Agent** can use RAG as one of its tools or capabilities.

    User Goal
         ↓
    AI Agent
         ↓
    Decide What to Do
         ↓
    RAG / Search / Database / API
         ↓
    Observe Result
         ↓
    Decide Next Action
         ↓
    Final Answer

So:

> **RAG provides relevant information to the LLM, while an agent can decide when and how to use tools to accomplish a goal.**

---

### AI Agent vs Chatbot

A basic chatbot may follow:

    User
      ↓
    LLM
      ↓
    Response

An AI agent can perform multiple actions:

    User
      ↓
    Agent
      ↓
    Reason
      ↓
    Tool
      ↓
    Result
      ↓
    Reason
      ↓
    Another Tool
      ↓
    Result
      ↓
    Final Response

The key difference is **autonomous action and tool usage**.

---

### Interview Answer

If the interviewer asks:

**"What is an AI Agent?"**

You can answer:

> **"An AI agent is an AI system, usually powered by an LLM, that can understand a goal, decide what actions are required, use external tools, observe the results, and continue taking actions until it completes the task. Tool calling, memory or state, and an agent loop are common components of agentic systems."**

---

### Short Version to Remember

    AI Agent
         ↓
    Understand Goal
         ↓
    Decide Action
         ↓
    Use Tool
         ↓
    Observe Result
         ↓
    Decide Again
         ↓
    Repeat
         ↓
    Complete Goal

**LLM = Generates and reasons about language**

**Tool = Performs an external action**

**Agent = Uses the LLM + tools + state to accomplish a goal**