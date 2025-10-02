# 🧩 LangSmith Tutorial — Module 1, Lesson 1: Tracing Basics

This lesson introduces **tracing** in LangSmith — a way to record, visualize, and debug your LLM application workflows using the `@traceable` decorator.  
The example builds a simple RAG (Retrieval-Augmented Generation) pipeline and instruments each step for tracing.

---

## 🚀 What I Learnt

### 🧠 1. `@traceable` Automatically Enables Tracing
- The `@traceable` decorator from **LangSmith** instruments any function to automatically log its inputs, outputs, and duration.  
- Every decorated function call (and its nested calls) becomes a **trace** or **run** in the LangSmith dashboard — no manual logging required.

---

### 🧩 2. Tracing Captures the Structure of Your RAG Pipeline
Since each step of the default pipeline is already decorated, I learned to create a **hierarchical trace** of function calls:

```text
langsmith_rag()   (main run)
├── retrieve_documents()
├── generate_response()
└── call_openai()
```
---

### 🗂️ 3. Metadata Gives More Context to Each Trace

The `@traceable` decorator allows us to attach **custom metadata** to each traced function.  
This metadata provides additional context that appears in your LangSmith dashboard and helps us organize or filter traces.

#### 💡 Example
```python
@traceable(metadata={"model_provider": "openai", "model_name": "gpt-4o-mini"})
def call_openai(messages: list[dict]):
    return openai_client.chat.completions.create(
        model="gpt-4o-mini",
        messages=messages,
    )

```

---
