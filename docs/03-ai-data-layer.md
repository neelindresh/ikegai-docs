# Layer 03: Agentic Tool

## Purpose

The "Hands" of the system. This layer contains the specific functions and APIs the Agentic Engine can call to interact with the real world.

## Key Components

### 1. Tool Definitions (Schemas)

* **Function Calling:** JSON schemas describing arguments (e.g., `get_weather(location: str)`).
* **API Wrappers:** Python/Node.js wrappers for third-party services (Salesforce, Jira, SQL).

### 2. Execution Sandbox

* **Environment Safety:** Tools run in isolated containers to prevent malicious code execution.
* **Result Parsing:** Standardizes tool outputs (JSON/Text) back into a format the Agentic Engine understands.
