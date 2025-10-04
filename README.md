# 🧩 LangSmith Tutorial 

- Author Name: Saket Kumar Sinha
- Credits: LangChain Community for providing the original GitHub repository.  
- Repository link: [intro-to-langsmith](https://github.com/langchain-ai/intro-to-langsmith)
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
### 🚀 What I Learned
- How to use `@traceable` from LangSmith to trace different types of operations:
  - `run_type="llm"` for Large Language Model calls.
  - `run_type="tool"` for custom helper functions.
  - `run_type="chain"` for chaining multiple steps.
- How to structure inputs for chat models using roles (`system`, `user`, `assistant`).
- How to format outputs so LangSmith can track responses properly.

---

### 🗂️ Changes I Made
- Created a simple **chat model** (`chat_model`) that returns a hardcoded assistant response for a table booking request.
- Added an **example streaming chat model** (`my_streaming_chat_model`) to demonstrate chunked responses and concatenation with `_reduce_chunks`.
- Implemented a **retriever function** (`retrieve_docs`) to show how documents could be returned in LangSmith’s expected format.
- Created a **tool + chain example** (`get_current_temperature` and `ask_about_the_weather`) that demonstrates:
  - Defining a tool function.
  - LLM requesting a tool call.
  - Returning the tool response back to the LLM and producing the final output.

---

### 💡 Example 

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
## Module 1, Lesson 3: Alternative Tracing methods
---
### 🚀 What I Learned

- Explored RunTree, wrapOpenAI, and Trace Context Manager as alternative methods for logging traces.
- Learned how RunTree can track workflows in more advanced scenarios.
- Understood how wrapOpenAI can integrate trace logging directly with OpenAI API calls.
- Observed how Trace Context Manager allows contextual trace logging within workflows.
---

### 🗂️ Changes I Made

- Used **wrap_openai** to create a wrapped OpenAI client, enabling trace logging for each API call.
- Implemented a **RunTree-based text pipeline** to trace a series of operations like string concatenation and word counting.
- Demonstrated how **child runs** in RunTree can capture inputs, outputs, and metadata for better observability.
- Showcased **end-to-end trace logging**, from sending prompts to OpenAI to processing text, making debugging and monitoring easier.
---

### 💡 Example 

```python
strings = ["LangSmith", "makes", "tracing", "easy"]
concatenated, word_count, run_tree = text_pipeline(strings)
print(f"Concatenated: {concatenated}, Word Count: {word_count}")
```
---
## Module 1, Lesson 4: Conversational Threads
---
### 🚀 What I Learned

- Learned how to use `@traceable` to automatically log and trace different steps in a RAG workflow.
- Understood how to structure a retrieval-augmented generation (RAG) pipeline with separate document retrieval and response generation functions.
- Observed how to format retrieved documents and system/user prompts before sending them to the OpenAI API.
- Saw how to integrate OpenAI LLM calls (`chat.completions.create`) within a traceable chain for reproducible and monitored question-answering.
---

### 🗂️ Changes I Made

- Renamed the main RAG function to `student_conversation_rag` to better reflect its purpose.
- Simplified the system prompt for clearer and shorter answers (max three sentences).
- Updated function and variable names for readability (`system_prompt` instead of `rag_system_prompt`).
- Ensured consistent traceability using `@traceable` decorators across all main steps (retrieval, response generation, LLM call).

---

### 💡 Example 

```python
@traceable(run_type="chain")
def student_conversation_rag(question: str):
    documents = retrieve_documents(question)
    response = generate_response(question, documents)
    return response.choices[0].message.content

if __name__ == "__main__":
    question1 = "Hey, can you explain what a binary search tree is?"
    answer1 = student_conversation_rag(question1)
    print("Answer:", answer1)
    
    question2 = "Why is it better than a normal array?"
    answer2 = student_conversation_rag(question2)
    print("Answer:", answer2)
```
---

## Module 2, Lesson 1: Creating Datasets
This project explores how to use the **LangSmith SDK** to create, manage, and utilize custom datasets for question-answer workflows.  
The focus was on understanding dataset creation, structuring inputs/outputs, and preparing data for retrieval-augmented generation (RAG) pipelines.

---

### 🚀 What I Learned
- Discovered how to **create custom datasets** using the **LangSmith SDK**.  
- Learned to **structure inputs and outputs** for **question–answer datasets**.  
- Observed how these datasets can be **used later in retrieval or RAG workflows** to improve model accuracy and relevance.  
---

### 🗂️ Changes I Made
- **Creates structured Q&A pairs** — Defines a list of chess-related question–answer examples to be used for dataset creation.  
- **Initializes a LangSmith client** — Connects to your LangSmith account using the `Client()` class.  
- **Formats data for upload** — Organizes questions into `inputs` and answers into `outputs` dictionaries as required by the LangSmith API.  
- **Uploads examples to a dataset** — Sends all Q&A examples to a specific dataset (identified by its `dataset_id`) using `client.create_examples()`.  
---

### 💡 Example 

```python
example_inputs = [
("How do I start a game of chess?", "To start a chess game, set up the board with white pieces on the first rank and black pieces on the eighth rank, placing rooks, knights, bishops, queen, and king in the correct positions."),
("What are the moves for a pawn?", "Pawns move forward one square, but on their first move they can move two squares. They capture diagonally one square forward."),
("How does the knight move?", "The knight moves in an L-shape: two squares in one direction and then one square perpendicular. It can jump over other pieces."),
("What is castling in chess?", "Castling is a special move where the king moves two squares towards a rook, and the rook moves to the square next to the king. It can only be done if neither piece has moved before and there are no pieces between them."),
("How does the queen move?", "The queen can move any number of squares horizontally, vertically, or diagonally."),
("What is check and checkmate?", "Check is when the king is under threat of capture. Checkmate is when the king is in check and has no legal moves to escape."),
("How do bishops move?", "Bishops move any number of squares diagonally and cannot jump over other pieces."),
("What are the rules for en passant?", "En passant allows a pawn to capture an opponent's pawn that has moved two squares forward from its starting position as if it had moved only one square."),
("How does a rook move?", "Rooks move any number of squares vertically or horizontally and cannot jump over other pieces."),
("How does a game of chess end?", "A game of chess ends with checkmate, stalemate, resignation, or draw by agreement or insufficient material."),
]
```

---

## Module 2, Lesson 2: Evaluating
---
### 🚀 What I Learned
- Learned to build evaluators for assessing similarity between outputs.  
- Explored leveraging LLMs to implement these automated evaluators.  
- Observed how evaluators can streamline and automate the assessment of responses or code logic.  
---

### 🗂️ Changes I Made

- Created a custom code logic evaluator using the LangSmith SDK to compare new solutions against reference solutions.  
- Structured inputs and outputs for coding problems, with `"problem"` as the input and `"solution"` as the output.  
- Implemented a scoring system that rates the logical correctness of a submitted solution on a scale from 1 (incorrect) to 10 (identical logic).  
- Demonstrated evaluation using a sample run for a prime-checking function and a reference solution for comparison.
---

### 💡 Example 

```python
sample_example = {
  "inputs": {
    "problem": "Write a function to check if a number is prime."
  },
  "outputs": {
    "solution": "def is_prime(n):\n    if n < 2: return False\n    for i in range(2, int(n**0.5)+1):\n        if n % i == 0:\n            return False\n    return True"
  },
  "metadata": {
    "dataset_split": [
      "AI generated",
      "base"
    ]
  }
}

logic_score = compare_code_logic_v2(sample_run, sample_example)
print(f"Code logic score: {logic_score}")
```

---
## Module 2, Lesson 3: Experiments
---
### 🚀 What I Learned
- Gained experience running experiments on datasets using LLMs with the LangSmith SDK.  
- Explored creating and using custom evaluators to assess LLM outputs.  
- Learned how to pass metadata to experiments for tracking model details or additional information.    
---

### 🗂️ Changes I Made
- Updated the dataset to use your custom "Chess Dataset" instead of the default dataset.  
- Converted the `examples` generator from `client.list_examples()` into a list to properly iterate over examples.  
- Modified the target function to safely access the "question" key using `.get()` to prevent KeyErrors.  
- Applied the custom evaluator `is_concise_enough` to your dataset examples to automatically assess the conciseness of outputs.
---

### 💡 Example 

```python
from langsmith import evaluate, Client

client = Client()
dataset_id = "Chess Dataset" 

examples = list(client.list_examples(dataset_id=dataset_id))
print(f"Number of examples: {len(examples)}")  

def is_concise_enough(reference_outputs: dict, outputs: dict) -> dict:
    score = len(outputs.get("output", "")) < 1.5 * len(reference_outputs.get("output", ""))
    return {"key": "is_concise", "score": int(score)}

def target_function(inputs: dict):
    question_key = "question"
    return langsmith_rag(inputs.get(question_key, "")) 

evaluate(
    target_function,
    data=examples, 
    evaluators=[is_concise_enough],
    experiment_prefix="gpt-4o"
)

```