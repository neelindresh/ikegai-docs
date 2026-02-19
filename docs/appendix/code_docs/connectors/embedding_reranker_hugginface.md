# HuggingFace Reranker (`HuggingFaceRerankerLoader`)

!!! abstract "Class Overview"
    `HuggingFaceRerankerLoader` is a factory class designed to optimize the initialization of LangChain's `HuggingFaceCrossEncoder`. By implementing a Singleton-style caching mechanism, it ensures that heavy cross-encoder models—which evaluate the relevance between pairs of texts—are only loaded into memory once, significantly reducing latency and memory overhead across your application.



## Core Architecture: Cross-Encoder Caching

While standard embedding models (bi-encoders) map text to vectors independently, a cross-encoder processes the query and the document simultaneously through a shared neural network to output a highly accurate similarity score. Because these models are computationally expensive, loading them repeatedly is a massive bottleneck.

Similar to our embedding loaders, this class intercepts object creation using the `__new__` dunder method:

* **Cache Check:** When instantiated, `__new__` checks the internal `_store` to see if the requested cross-encoder model already exists in memory.
* **First-Time Load:** If absent, it initializes a `HuggingFaceCrossEncoder` object (downloading/loading the weights) and caches it.
* **Subsequent Loads:** If present, it bypasses initialization and instantly returns the cached LangChain model object.

---

## Initialization Parameters

| Parameter | Type | Description |
| :--- | :--- | :--- |
| `model_name` | `str` | The HuggingFace repository ID for the cross-encoder (e.g., `BAAI/bge-reranker-large` or `cross-encoder/ms-marco-MiniLM-L-6-v2`). |
| `model_kwargs` | `dict` | (Optional) Hardware configuration arguments (e.g., `{"device": "cuda"}`). |

---

## Usage Example (with LangChain)

Because this factory returns the raw LangChain `HuggingFaceCrossEncoder` object, you can plug it directly into LangChain's `CrossEncoderReranker` to compress and reorder your retrieved documents.

```python
from connectors.embeddings.huggingface_reranker import HuggingFaceRerankerLoader
from langchain.retrievers.document_compressors import CrossEncoderReranker
from langchain.retrievers import ContextualCompressionRetriever

# 1. Load the cross-encoder model (caches on first run)
hf_cross_encoder = HuggingFaceRerankerLoader(
    model_name="BAAI/bge-reranker-large",
    model_kwargs={"device": "cpu"}
)

# 2. Wrap it in a LangChain Document Compressor
compressor = CrossEncoderReranker(model=hf_cross_encoder, top_n=3)

# 3. Attach it to your existing base retriever (e.g., Qdrant or Chroma)
compression_retriever = ContextualCompressionRetriever(
    base_compressor=compressor,
    base_retriever=your_base_vector_retriever
)

# 4. Invoke the pipeline (documents are fetched, then intelligently reranked)
final_docs = compression_retriever.invoke("What is the architecture of the agentic engine?")

```