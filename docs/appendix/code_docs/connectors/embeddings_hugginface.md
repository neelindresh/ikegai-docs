# HuggingFace Embeddings (`HuggingFaceEmbeddingsLoader`)

!!! abstract "Class Overview"
    `HuggingFaceEmbeddingsLoader` is a specialized factory class that inherits from `EmbeddingsFunction`. It provides a highly optimized wrapper around LangChain's `HuggingFaceEmbeddings`. By utilizing a class-level caching mechanism, it ensures that heavy open-source embedding models (like `sentence-transformers`) are loaded into the application's RAM/VRAM only once.

## Core Architecture: Local Model Caching

Loading local HuggingFace models can be slow and memory-intensive. Similar to our Azure implementation, this class intercepts the creation process using the Python `__new__` dunder method to act as a **Singleton-style Factory**.

1. **Cache Check:** When called, `__new__` checks an internal class-level dictionary (`_store`) to see if an instance of the requested `model_name` already exists in memory.
2. **First-Time Load:** If the model is not in the cache, it initializes a brand new LangChain `HuggingFaceEmbeddings` object (which loads the weights into memory), saves it to `_store`, and returns it.
3. **Subsequent Loads:** If the model is already in the cache, it bypasses initialization entirely and instantly returns the existing model object.

!!! warning "Python Object Returns"
    Just like the Azure loader, `__new__` explicitly returns an instance of the LangChain `HuggingFaceEmbeddings` object, effectively bypassing this loader's `__init__` method.

---

## Initialization Parameters

| Parameter | Type | Description |
| :--- | :--- | :--- |
| `model_name` | `str` | The specific HuggingFace model repo ID (e.g., `sentence-transformers/all-MiniLM-L6-v2`). This acts as the unique cache key. |
| `model_kwargs` | `dict` | (Optional) Dictionary of keyword arguments to pass to the model (e.g., `{"device": "cuda"}`). |
| `encode_kwargs` | `dict` | (Optional) Dictionary of keyword arguments to pass when encoding text (e.g., `{"normalize_embeddings": True}`). |

---

## Usage Example

```python
from connectors.embeddings.huggingface import HuggingFaceEmbeddingsLoader

# 1. First invocation: Downloads (if necessary) and loads the heavy model into memory
hf_embeddings = HuggingFaceEmbeddingsLoader(
    model_name="sentence-transformers/all-MiniLM-L6-v2",
    model_kwargs={"device": "cpu"},
    encode_kwargs={"normalize_embeddings": False}
)
# Console Output: Loading sentence-transformers/all-MiniLM-L6-v2 for the first time...

# 2. Second invocation elsewhere: Instantly fetches from RAM without reloading weights
same_hf_embeddings = HuggingFaceEmbeddingsLoader(
    model_name="sentence-transformers/all-MiniLM-L6-v2"
)
# Console Output: Fetching sentence-transformers/all-MiniLM-L6-v2 from cache...

# 3. Use standard LangChain methods
vectors = hf_embeddings.embed_query("How does caching improve performance?")
```