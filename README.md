# Agentic AI in Google Colab — Use Case (Factorial, UUID)

![Python](https://img.shields.io/badge/Python-3.10%2B-blue)
![Google Colab](https://img.shields.io/badge/Run-Google%20Colab-orange)
![LangChain](https://img.shields.io/badge/LangChain-Agent%20Framework-green)
![Transformers](https://img.shields.io/badge/Hugging%20Face-Transformers-yellow)
![Status](https://img.shields.io/badge/Status-Learning%20Project-informational)

A beginner-friendly **Agentic AI** project built in **Google Colab** that shows how an LLM can reason, choose a tool, execute Python code, observe the result, and return a final answer.

This repository uses a simple mathematical task — **calculating the factorial of 6** — to demonstrate the core architecture of an AI agent.

---

## Table of Contents

- [Overview](#overview)
- [Why This Project](#why-this-project)
- [What This Project Demonstrates](#what-this-project-demonstrates)
- [Architecture](#architecture)
- [Tech Stack](#tech-stack)
- [Project File](#project-file)
- [How the Code Works](#how-the-code-works)
- [How to Run](#how-to-run)
- [Expected Output](#expected-output)
- [What This Project Teaches](#what-this-project-teaches)
- [Mental Model](#mental-model)
- [Known Notes](#known-notes)
- [Who This Repository Is For](#who-this-repository-is-for)
- [Author](#author)

---

## Overview

This project is a foundational hands-on example of **Agentic AI**.

Instead of asking a language model to directly answer a math question, this notebook turns the model into an **agent** that can:

1. receive a task,
2. reason about what to do,
3. decide whether to use a tool,
4. execute code using Python,
5. observe the result,
6. return the final answer.

The use case is intentionally small so the focus stays on the **agent design pattern**, not on business complexity.

---

## Why This Project

When learning Agentic AI, it is useful to start with a very small and clear example.

A factorial problem is a good first use case because it helps demonstrate:

- how an agent is structured,
- how a tool is attached to the agent,
- how prompts guide tool usage,
- how the executor manages the reasoning loop,
- and how the model can delegate computation to Python.

This repository is meant to build a strong foundation before moving to larger use cases such as APIs, cloud automation, Jira workflows, or RAG systems.

---

## What This Project Demonstrates

This project demonstrates the following core ideas:

- **LLM as reasoning engine**
- **Python tool as external capability**
- **Prompt-driven agent behavior**
- **ReAct-style interaction pattern**
- **Executor-managed action loop**
- **Beginner-friendly Colab setup**
- **Custom streaming patch for stable execution**

---

## Architecture

### High-Level Flow

```text
User Task
   ↓
Prompt Template
   ↓
Language Model
   ↓
Reasoning
   ↓
Choose Tool
   ↓
Run Python Tool
   ↓
Observe Result
   ↓
Final Answer

This project follows a simple agent loop where the language model reasons about the task, decides whether a tool is needed, executes the tool if required, observes the result, and then returns the final answer.
```
### Agent Loop

```mermaid
flowchart TD
    A[User Input] --> B[Prompt Template]
    B --> C[LLM Reasoning]
    C --> D{Need Python Tool?}
    D -- Yes --> E[PythonREPLTool]
    E --> F[Observation]
    F --> C
    D -- No --> G[Final Answer]
```

## Tech Stack

This project is built using the following tools and frameworks:

- **Python** — core programming language
- **Google Colab** — notebook environment for development and execution
- **LangChain Classic** — agent framework components
- **LangChain Experimental** — experimental tools such as `PythonREPLTool`
- **LangChain Hugging Face** — integration between LangChain and Hugging Face models
- **Hugging Face Transformers** — model and tokenizer loading
- **Accelerate** — model execution support
- **Qwen/Qwen2.5-1.5B-Instruct** — instruction-tuned language model used as the reasoning engine


## Project File

```text
main.py
```

## How the Code Works

The program follows this structure:

### 1. Install dependencies
The notebook installs the required LangChain and Hugging Face libraries needed to run the agent and model pipeline.

### 2. Import required modules
It imports:
- Hugging Face model and tokenizer utilities
- LangChain agent components
- Python REPL tool
- prompt utilities
- streaming-related classes

### 3. Patch the Hugging Face streaming behavior
A custom class named `PatchedHuggingFacePipeline` extends `HuggingFacePipeline`.

Why this matters:
- the default streaming path can sometimes fail in Colab-like environments
- this patched version uses a safer streaming setup
- it helps the agent run more reliably

### 4. Load the model
The code loads (This model is used as the reasoning engine for the agent):

```text
Qwen/Qwen2.5-1.5B-Instruct
```

### 5. Create a text-generation pipeline
The Hugging Face model and tokenizer are wrapped inside a text-generation pipeline with controlled generation settings such as:

- `max_new_tokens`
- `do_sample=False`
- `return_full_text=False`

These settings help make the model output more predictable and suitable for agent execution.

### 6. Wrap the model for LangChain
The Hugging Face pipeline is then wrapped in a LangChain-compatible LLM class so it can be used directly by the agent framework.

### 7. Add a tool
The agent is given access to:

```python
PythonREPLTool()
```

### 8. Define the prompt
A structured prompt tells the agent:

- how to think,
- how to format actions,
- how to call tools,
- when to return a final answer.

This prompt uses a **ReAct-style** pattern:

- **Thought**
- **Action**
- **Action Input**
- **Final Answer**

### 9. Create the agent
The code uses `create_react_agent(...)` to combine:

- the language model,
- the available tools,
- and the prompt template.

This creates the core decision-making agent.

### 10. Create the executor
The `AgentExecutor` runs the agent step by step and manages:

- reasoning flow,
- tool execution,
- parsing errors,
- maximum iteration limits,
- and verbose logging.

### 11. Run the use case
The input task is:

```python
"Use Python to calculate factorial of 6"
```

## How to Run

### Option 1: Run in Google Colab

1. Open Google Colab.
2. Upload or paste the code from `ai_agent_math_factorial.py`.
3. Run the installation cells.
4. Run the remaining cells in sequence.
5. Wait for the model to download and initialize.
6. Observe the agent’s reasoning steps and final output.

### Option 2: Run from the exported script

If you want to run the code as a Python script, first move the notebook-specific installation commands into your local or virtual environment setup.

## Expected Output

The final answer for this use case should be:

```text
720
```
Because:

```text
6! = 6 × 5 × 4 × 3 × 2 × 1 = 720
```

## What This Project Teaches

This project is useful for understanding:

- the difference between a traditional chatbot and an AI agent,
- how a language model can use external tools,
- how prompts influence agent behavior,
- how LangChain agents are structured,
- how a Python tool can be integrated into an agent workflow,
- how to design a beginner-friendly Agentic AI experiment in Google Colab.

## Mental Model

A simple way to understand the overall architecture is:

- **LLM** = the brain
- **Tool** = the hands
- **Prompt** = the instructions
- **Agent** = the decision-maker
- **Executor** = the workflow runner

## Known Notes

- The initial model download may take some time in Google Colab.
- Hugging Face authentication is optional for public models, but adding an `HF_TOKEN` can improve download reliability and speed.
- The custom patched pipeline is included to make streaming more stable during agent execution.
- This is a learning-focused project, so the use case is intentionally small and easy to follow.

## Who This Repository Is For

This project is ideal for:

- beginners learning the fundamentals of Agentic AI,
- developers exploring LangChain-based agents,
- students learning how tool-enabled LLMs work,
- engineers building a foundation before moving to more advanced agent systems.

## Author

Created as a starter repository for learning and experimenting with **Agentic AI** using Python, Google Colab, and tool-enabled language models.
