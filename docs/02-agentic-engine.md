# Layer 02: Agentic Engine

## Purpose

The "Brain" of the system. This layer determines *what* to do based on the user intent and available context. It does not execute actions directly but plans them.

## Key Components

### 1. The Planner (Reasoning Loop)

* **ReAct / CoT:** Uses Chain-of-Thought reasoning to break complex user queries into sub-tasks.
* **Prompt Management:** System prompts that define the agent's persona and constraints.

### 2. Context Window Management

* **Token Optimization:** Summarizes conversation history to fit within the LLM's context window.
* **Router:** Decides which specific LLM (e.g., GPT-4 vs. Llama 3) handles the query based on complexity.
