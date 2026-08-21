# Hallucination

### Simple Definition

**Hallucination** occurs when an LLM generates information that sounds correct and confident but is **incorrect, made up, or not supported by reliable information**.

In simple words:

> **An LLM can sometimes generate a convincing answer even when it does not actually know the correct answer. This is called hallucination.**

---

### Example

Suppose you ask:

    "Who invented JavaScript?"

The correct answer is:

    Brendan Eich

But if the model incorrectly responds:

    "JavaScript was invented by James Gosling in 1995."

The answer sounds plausible, but it is **incorrect**.

This is an example of an LLM hallucination.

---

### Why Do LLMs Hallucinate?

LLMs primarily generate responses by predicting the **next token based on learned patterns and context**.

They do not automatically verify every generated statement against a reliable source.

Hallucinations can happen because of:

- Insufficient information in the model's context
- Ambiguous questions
- Outdated knowledge
- Poor-quality training data
- Lack of reliable external information
- The model generating a plausible but incorrect continuation
- Incorrect or irrelevant retrieved information in RAG

---

### Hallucination in RAG

RAG can help **reduce hallucinations** by providing relevant external information to the LLM.

Without RAG:

    User Question
         ↓
    LLM
         ↓
    Generated Answer
         ↓
    Possible Hallucination

With RAG:

    User Question
         ↓
    Retrieve Relevant Documents
         ↓
    Add Retrieved Context
         ↓
    LLM
         ↓
    Grounded Answer

The retrieved documents provide information that the model can use when generating its response.

However:

> **RAG does not completely eliminate hallucinations.**

If the retrieved documents are incorrect, irrelevant, incomplete, or poorly retrieved, the model can still produce an incorrect answer.

---

### How Can We Reduce Hallucinations?

Common techniques include:

#### 1. Better Prompting

Give the model clear instructions.

Example:

    "Answer only using the provided context.
     If the answer is not present, say:
     'I don't know based on the provided information.'"

---

#### 2. RAG

Retrieve relevant information from external sources and provide it to the LLM as context.

    User Question
         ↓
    Retrieval
         ↓
    Relevant Context
         ↓
    LLM
         ↓
    Answer

---

#### 3. Better Retrieval

In RAG systems, retrieving the **right documents and chunks** is critical.

Techniques include:

- Better chunking
- Metadata filtering
- Hybrid search
- Reranking
- Query transformation

---

#### 4. Source Verification

Applications can provide citations or sources so users can verify the generated information.

    LLM Answer
         +
    Source References
         ↓
    User Verification

---

#### 5. Lower Randomness

Generation settings such as **temperature** can affect how predictable or diverse the model's output is.

Lower temperature generally produces more deterministic responses, but it **does not guarantee factual correctness**.

---

### Hallucination vs Normal Error

A normal error may happen because the model misunderstood the question or made a mistake.

A hallucination specifically refers to **fabricated or unsupported information presented as if it were factual**.

---

### Interview Answer

If the interviewer asks:

**"What is hallucination in an LLM?"**

You can answer:

> **"Hallucination is when an LLM generates information that sounds convincing but is factually incorrect or unsupported by the available context. LLMs generate responses based on learned patterns rather than automatically verifying every fact. We can reduce hallucinations using techniques such as RAG, better prompting, reliable retrieval, source verification, and appropriate generation settings."**

---

### Short Version to Remember

    Hallucination
         ↓
    Confident but incorrect / unsupported information

    Common Causes
         ↓
    Missing Context + Poor Retrieval + Ambiguous Input
         ↓
    Possible Incorrect Output

    Reduce It
         ↓
    Better Prompts + RAG + Better Retrieval + Verification