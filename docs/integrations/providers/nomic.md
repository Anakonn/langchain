---
canonical: https://python.langchain.com/v0.2/docs/integrations/providers/nomic/
custom_edit_url: https://github.com/langchain-ai/langchain/edit/master/docs/docs/integrations/providers/nomic.ipynb
---

# Nomic

Nomic currently offers two products:

- Atlas: their Visual Data Engine
- GPT4All: their Open Source Edge Language Model Ecosystem

The Nomic integration exists in its own [partner package](https://pypi.org/project/langchain-nomic/). You can install it with:


```python
%pip install -qU langchain-nomic
```

Currently, you can import their hosted [embedding model](/docs/integrations/text_embedding/nomic) as follows:


```python
<!--IMPORTS:[{"imported": "NomicEmbeddings", "source": "langchain_nomic", "docs": "https://api.python.langchain.com/en/latest/embeddings/langchain_nomic.embeddings.NomicEmbeddings.html", "title": "Nomic"}]-->
from langchain_nomic import NomicEmbeddings
```