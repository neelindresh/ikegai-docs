# Azure Document Intelligence Processor (`AzureDocProcessor`)

!!! abstract "Class Overview"
    `AzureDocProcessor` is a dedicated wrapper for the Azure Document Intelligence API. It utilizes the `prebuilt-layout` model to analyze complex documents (PDFs, images) and extract text, tables, and structural elements directly into **Markdown** format. This makes it an ideal precursor for LLM ingestion and Vector DB chunking.

## Initialization

The class requires an active Azure endpoint and API key to instantiate the `DocumentIntelligenceClient`.

**`__init__(self, endpoint: str, key: str)`**

* **`endpoint`**: The URL of your Azure Document Intelligence resource.
* **`key`**: The corresponding API access key.

---

## Class Methods

### 1. Full Document Extraction
**`analyze_layout_with_markdown(self, file_path: str) -> str`**

Reads a local document and returns the entire parsed content as a single, continuous Markdown string.

* **Arguments:**
    * `file_path` (str): The local path to the file (e.g., PDF, PNG, JPEG) to be analyzed.
* **Returns:** * `str`: The complete Markdown representation of the document.
* **Use Case:** Best suited for smaller documents or when you want the LangChain framework to handle all of the recursive text chunking dynamically.

### 2. Page-Wise Extraction
**`analyze_layout_with_markdown_pagewise(self, file_path: str) -> list[dict]`**

Extracts the document and intelligently splits the Markdown output strictly by the document's original page boundaries. 

!!! info "How Page Slicing Works"
    The Azure API returns a single giant string for the whole document alongside an array of page objects. This method iterates through `result.pages` and uses the `span` attributes (offset and length) to precisely slice the global Markdown string into page-specific chunks.

* **Arguments:**
    * `file_path` (str): The local path to the file.
* **Returns:** * A list of dictionaries, where each dictionary represents a single page.
    * **Format:** `[{"page_no": 1, "content": "## Header\n\nPage 1 text..."}]`
* **Use Case:** Essential for Retrieval-Augmented Generation (RAG) pipelines where maintaining exact page-level citations and metadata is required for the LLM's response.

---

## Usage Example

```python
from doc_processors import AzureDocProcessor

# Initialize the processor
processor = AzureDocProcessor(
    endpoint="[https://your-resource.cognitiveservices.azure.com/](https://your-resource.cognitiveservices.azure.com/)",
    key="your_azure_api_key"
)

file_to_process = "assets/documents/financial_report_2025.pdf"

# Example 1: Get the full document as one string
full_markdown = processor.analyze_layout_with_markdown(file_to_process)
print(full_markdown[:500]) # Print the first 500 characters

# Example 2: Get page-by-page chunks
paged_markdown = processor.analyze_layout_with_markdown_pagewise(file_to_process)

for page in paged_markdown:
    print(f"--- Document Page: {page['page_no']} ---")
    print(page['content'])
```