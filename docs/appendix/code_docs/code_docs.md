# Connector Code Reference

!!! abstract "Overview"
    This section contains the detailed, code-level documentation for the individual modules within the IKEGAI Connector Framework. Here you will find class definitions, method signatures, and implementation guides for interacting with our supported databases, document parsers, and external APIs.

## Available Modules

Select a module below to view its specific developer documentation and usage examples.

### 🗄️ Database Connectors
Modules responsible for stateful, structured data interactions, connection pooling, and standard CRUD operations.

* **[MongoDB Connector (`MongoConnect`)](connectors/mongo.md)** *Details the multiton-pattern MongoDB wrapper used for resilient, efficient database connections and automatic ObjectId handling.*

### 📄 Document Processors
Modules responsible for unstructured file parsing, OCR, and layout analysis before passing data to the LLM and embedding layers.

* **[Azure Document Intelligence (`AzureDocProcessor`)](connectors/azuredoc.md)** *Details the Azure AI wrapper used to extract clean Markdown, tables, and page-wise text chunks from complex documents like PDFs.*

### 🧠 Embeddings & Rerankers
Modules responsible for transforming text into vector representations using optimized caching and factory patterns.

* **[Azure OpenAI Embeddings (`AzureOpenAIEmbeddingsLoader`)](connectors/embedding_azureopenai.md)** *Details the Singleton-style factory class used to load and cache LangChain Azure OpenAI embedding models to optimize application memory and performance.*
  
* **[HuggingFace Embeddings (`HuggingFaceEmbeddingsLoader`)](connectors/embeddings_hugginface.md)** *Details the local caching factory for standard HuggingFace bi-encoder embedding models.*


* **[HuggingFace Reranker (`HuggingFaceRerankerLoader`)](connectors/embedding_reranker_hugginface.md)** *Details the local caching factory for heavy HuggingFace cross-encoder models used in context compression and document reranking.*


---

!!! tip "Expanding the Framework"
    This directory will be continuously updated as new code references are generated for our wider ecosystem of supported data sources (e.g., Azure SQL, Postgres, Amazon S3, and standard HTTP web scrapers).