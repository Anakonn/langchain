---
canonical: https://python.langchain.com/v0.2/docs/integrations/text_embedding/llamacpp/
custom_edit_url: https://github.com/langchain-ai/langchain/edit/master/docs/docs/integrations/text_embedding/llamacpp.ipynb
---

# Llama.cpp

>[llama.cpp python](https://github.com/abetlen/llama-cpp-python) library is a simple Python bindings for `@ggerganov`
>[llama.cpp](https://github.com/ggerganov/llama.cpp).
>
>This package provides:
>
> - Low-level access to C API via ctypes interface.
> - High-level Python API for text completion
>   - `OpenAI`-like API
>   - `LangChain` compatibility
>   - `LlamaIndex` compatibility
> - OpenAI compatible web server
>   - Local Copilot replacement
>   - Function Calling support
>   - Vision API support
>   - Multiple Models



```python
%pip install --upgrade --quiet  llama-cpp-python
```


```python
<!--IMPORTS:[{"imported": "LlamaCppEmbeddings", "source": "langchain_community.embeddings", "docs": "https://api.python.langchain.com/en/latest/embeddings/langchain_community.embeddings.llamacpp.LlamaCppEmbeddings.html", "title": "Llama.cpp"}]-->
from langchain_community.embeddings import LlamaCppEmbeddings
```


```python
llama = LlamaCppEmbeddings(model_path="/path/to/model/ggml-model-q4_0.bin")
```


```python
text = "This is a test document."
```


```python
query_result = llama.embed_query(text)
```


```python
doc_result = llama.embed_documents([text])
```


## Related

- Embedding model [conceptual guide](/docs/concepts/#embedding-models)
- Embedding model [how-to guides](/docs/how_to/#embedding-models)