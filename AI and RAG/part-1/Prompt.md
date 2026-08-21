# Prompt / System Prompt / User Prompt

### What is a Prompt?

A **prompt** is the input or instruction given to an LLM to tell it what to do or what information to process.

A prompt can contain:

- Instructions
- Questions
- Context
- Examples
- Constraints
- Data

In simple words:

> **A prompt tells the LLM what you want it to do and provides the information it needs to perform the task.**

---

### Example

    Prompt:
    "Explain React in simple terms."

The LLM receives the prompt and generates a response based on it.

---

# Types of Prompts

In LLM applications, you will commonly encounter:

1. **System Prompt**
2. **User Prompt**

---

## 1. System Prompt

A **system prompt** provides the high-level instructions, rules, behavior, or role that the LLM should follow.

It is usually defined by the **application or developer**, rather than directly by the end user.

Example:

    You are a helpful technical interviewer.
    Ask one question at a time.
    Keep your answers concise.
    Explain technical concepts with examples.

The system prompt establishes how the model should behave.

---

### Why Use a System Prompt?

System prompts are useful for controlling:

- Model behavior
- Role
- Response format
- Tone
- Rules
- Restrictions
- Output requirements

Example:

    System Prompt:
    "You are an AI interview coach.
     Give concise technical feedback.
     Do not provide answers longer than 200 words."

---

## 2. User Prompt

A **user prompt** is the instruction or question provided by the user.

Example:

    User:
    "Explain what RAG is."

The application sends the user's request to the LLM along with the relevant system instructions and context.

---

### System Prompt vs User Prompt

| System Prompt | User Prompt |
|---|---|
| Defines model behavior | Defines the user's request |
| Usually provided by the application/developer | Provided by the user |
| Sets rules and instructions | Asks a question or gives a task |
| Establishes role and constraints | Provides the immediate task |
| Higher-level instruction | Task-specific instruction |

---

### Example Together

Imagine an AI interview application.

**System Prompt:**

    You are an AI interviewer.
    Ask technical questions related to React and Node.js.
    Ask one question at a time.
    Evaluate the candidate's answer.

**User Prompt:**

    "Start my interview."

The model combines the instructions and user request to generate an appropriate response.

---

# Prompt + Context

A prompt can also include additional context.

For example, in a RAG application:

    System Prompt
         +
    Retrieved Documents
         +
    User Prompt
         ↓
    LLM
         ↓
    Answer

Example:

**System Prompt:**

    Answer questions using the provided context.
    If the answer is not present in the context, say you don't know.

**Retrieved Context:**

    React is a JavaScript library for building user interfaces.

**User Prompt:**

    "What is React?"

The LLM uses the instructions, retrieved context, and user question to generate the answer.

---

# Prompt in a Real Application

A typical LLM application can have:

    System Prompt
         ↓
    Application Context
         ↓
    RAG Context
         ↓
    User Prompt
         ↓
    LLM
         ↓
    Generated Response

For example:

    System:
    "You are an AI interview coach."

    Context:
    "Candidate has experience with React and Node.js."

    User:
    "Ask me a backend interview question."

    ↓

    LLM

    ↓

    "Explain the difference between authentication
     and authorization."

---

# Why Are Prompts Important?

The quality and clarity of instructions can significantly affect the model's output.

A good prompt should clearly define:

- What the model should do
- What information it should use
- What format the response should follow
- Any important constraints

For example:

**Weak Prompt:**

    Tell me about RAG.

**Better Prompt:**

    Explain RAG in simple terms.
    Include its architecture, main components,
    and one real-world example.

The second prompt gives the model more specific instructions.

---

### Interview Answer

If the interviewer asks:

**"What is the difference between a system prompt and a user prompt?"**

You can answer:

> **"A system prompt defines the model's behavior, role, rules, and constraints, and is usually provided by the application or developer. A user prompt contains the user's actual question or task. For example, in an AI interview application, the system prompt could instruct the model to behave as an interviewer, while the user prompt could ask it to start the interview."**

---

### Short Version to Remember

    System Prompt
    → Defines behavior and rules

    User Prompt
    → Defines the user's task or question

    Context
    → Provides additional information

    System Prompt + Context + User Prompt
                    ↓
                   LLM
                    ↓
                Response