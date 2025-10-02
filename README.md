# 🧩 LangSmith Tutorial 
---
## Module 1, Lesson 1: Tracing Basics

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

## Module 1, Lesson 2: Different types of runs
---
## 🚀 What I Learned
- How to use `@traceable` from LangSmith to trace different types of operations:
  - `run_type="llm"` for Large Language Model calls.
  - `run_type="tool"` for custom helper functions.
  - `run_type="chain"` for chaining multiple steps.
- How to structure inputs for chat models using roles (`system`, `user`, `assistant`).
- How to format outputs so LangSmith can track responses properly.

---

## 🗂️ Changes I Made
- Created a simple **chat model** (`chat_model`) that returns a hardcoded assistant response for a table booking request.
- Added an **example streaming chat model** (`my_streaming_chat_model`) to demonstrate chunked responses and concatenation with `_reduce_chunks`.
- Implemented a **retriever function** (`retrieve_docs`) to show how documents could be returned in LangSmith’s expected format.
- Created a **tool + chain example** (`get_current_temperature` and `ask_about_the_weather`) that demonstrates:
  - Defining a tool function.
  - LLM requesting a tool call.
  - Returning the tool response back to the LLM and producing the final output.

---

## 💡 Example Usage

```python
inputs = [
    {"role": "system", "content": "You are a helpful assistant."},
    {"role": "user", "content": "I'd like to book a table for two."},
]

output = chat_model(inputs)
print(output["choices"][0]["message"]["content"])
# Output: "Sure, what time would you like to book the table for?"

```
---
## Module 1, Lesson 2: Different types of runs
---
## 🚀 What I Learned

- Explored RunTree, wrapOpenAI, and Trace Context Manager as alternative methods for logging traces.
- Learned how RunTree can track workflows in more advanced scenarios.
- Understood how wrapOpenAI can integrate trace logging directly with OpenAI API calls.
- Observed how Trace Context Manager allows contextual trace logging within workflows.
---

## 🗂️ Changes I Made

- Used **wrap_openai** to create a wrapped OpenAI client, enabling trace logging for each API call.
- Implemented a **RunTree-based text pipeline** to trace a series of operations like string concatenation and word counting.
- Demonstrated how **child runs** in RunTree can capture inputs, outputs, and metadata for better observability.
- Showcased **end-to-end trace logging**, from sending prompts to OpenAI to processing text, making debugging and monitoring easier.
---

### 💡 Example Usage

```python
strings = ["LangSmith", "makes", "tracing", "easy"]
concatenated, word_count, run_tree = text_pipeline(strings)
print(f"Concatenated: {concatenated}, Word Count: {word_count}")
```

