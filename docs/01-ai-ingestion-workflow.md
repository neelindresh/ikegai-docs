# Layer 01: AI Ingestion & Workflow

## Purpose

This layer handles the entry of raw data into the system and manages the workflow state transitions. It ensures that unstructured data (PDFs, Logs, Emails) is normalized before reaching the agent.

## Key Components

### 1. Ingestion Pipelines

* **Loaders:** Extract text from various sources (S3, Azure Blob, Web Scrapers).
* **Chunking Strategy:** Splits text into manageable tokens (e.g., recursive character splitting).
* **Embedding Generation:** Converts text chunks into vectors using embedding models (e.g., OpenAI, Cohere).

### 2. Workflow Orchestration

* **State Management:** Tracks the progress of a request through the system.
* **DAG Definition:** Defines the Directed Acyclic Graph for data flow.
