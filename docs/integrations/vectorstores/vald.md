---
canonical: https://python.langchain.com/v0.2/docs/integrations/vectorstores/vald/
custom_edit_url: https://github.com/langchain-ai/langchain/edit/master/docs/docs/integrations/vectorstores/vald.ipynb
---

# Vald

> [Vald](https://github.com/vdaas/vald) is a highly scalable distributed fast approximate nearest neighbor (ANN) dense vector search engine.

This notebook shows how to use functionality related to the `Vald` database.

To run this notebook you need a running Vald cluster.
Check [Get Started](https://github.com/vdaas/vald#get-started) for more information.

See the [installation instructions](https://github.com/vdaas/vald-client-python#install).


```python
%pip install --upgrade --quiet  vald-client-python langchain-community
```

## Basic Example


```python
<!--IMPORTS:[{"imported": "TextLoader", "source": "langchain_community.document_loaders", "docs": "https://api.python.langchain.com/en/latest/document_loaders/langchain_community.document_loaders.text.TextLoader.html", "title": "Vald"}, {"imported": "Vald", "source": "langchain_community.vectorstores", "docs": "https://api.python.langchain.com/en/latest/vectorstores/langchain_community.vectorstores.vald.Vald.html", "title": "Vald"}, {"imported": "HuggingFaceEmbeddings", "source": "langchain_huggingface", "docs": "https://api.python.langchain.com/en/latest/embeddings/langchain_huggingface.embeddings.huggingface.HuggingFaceEmbeddings.html", "title": "Vald"}, {"imported": "CharacterTextSplitter", "source": "langchain_text_splitters", "docs": "https://api.python.langchain.com/en/latest/character/langchain_text_splitters.character.CharacterTextSplitter.html", "title": "Vald"}]-->
from langchain_community.document_loaders import TextLoader
from langchain_community.vectorstores import Vald
from langchain_huggingface import HuggingFaceEmbeddings
from langchain_text_splitters import CharacterTextSplitter

raw_documents = TextLoader("state_of_the_union.txt").load()
text_splitter = CharacterTextSplitter(chunk_size=1000, chunk_overlap=0)
documents = text_splitter.split_documents(raw_documents)
embeddings = HuggingFaceEmbeddings()
db = Vald.from_documents(documents, embeddings, host="localhost", port=8080)
```


```python
query = "What did the president say about Ketanji Brown Jackson"
docs = db.similarity_search(query)
docs[0].page_content
```

### Similarity search by vector


```python
embedding_vector = embeddings.embed_query(query)
docs = db.similarity_search_by_vector(embedding_vector)
docs[0].page_content
```

### Similarity search with score


```python
docs_and_scores = db.similarity_search_with_score(query)
docs_and_scores[0]
```

## Maximal Marginal Relevance Search (MMR)

In addition to using similarity search in the retriever object, you can also use `mmr` as retriever.


```python
retriever = db.as_retriever(search_type="mmr")
retriever.invoke(query)
```

Or use `max_marginal_relevance_search` directly:


```python
db.max_marginal_relevance_search(query, k=2, fetch_k=10)
```

## Example of using secure connection
In order to run this notebook, it is necessary to run a Vald cluster with secure connection.

Here is an example of a Vald cluster with the following configuration using [Athenz](https://github.com/AthenZ/athenz) authentication.

ingress(TLS) -> [authorization-proxy](https://github.com/AthenZ/authorization-proxy)(Check athenz-role-auth in grpc metadata) -> vald-lb-gateway


```python
import grpc

with open("test_root_cacert.crt", "rb") as root:
    credentials = grpc.ssl_channel_credentials(root_certificates=root.read())

# Refresh is required for server use
with open(".ztoken", "rb") as ztoken:
    token = ztoken.read().strip()

metadata = [(b"athenz-role-auth", token)]
```


```python
<!--IMPORTS:[{"imported": "TextLoader", "source": "langchain_community.document_loaders", "docs": "https://api.python.langchain.com/en/latest/document_loaders/langchain_community.document_loaders.text.TextLoader.html", "title": "Vald"}, {"imported": "Vald", "source": "langchain_community.vectorstores", "docs": "https://api.python.langchain.com/en/latest/vectorstores/langchain_community.vectorstores.vald.Vald.html", "title": "Vald"}, {"imported": "HuggingFaceEmbeddings", "source": "langchain_huggingface", "docs": "https://api.python.langchain.com/en/latest/embeddings/langchain_huggingface.embeddings.huggingface.HuggingFaceEmbeddings.html", "title": "Vald"}, {"imported": "CharacterTextSplitter", "source": "langchain_text_splitters", "docs": "https://api.python.langchain.com/en/latest/character/langchain_text_splitters.character.CharacterTextSplitter.html", "title": "Vald"}]-->
from langchain_community.document_loaders import TextLoader
from langchain_community.vectorstores import Vald
from langchain_huggingface import HuggingFaceEmbeddings
from langchain_text_splitters import CharacterTextSplitter

raw_documents = TextLoader("state_of_the_union.txt").load()
text_splitter = CharacterTextSplitter(chunk_size=1000, chunk_overlap=0)
documents = text_splitter.split_documents(raw_documents)
embeddings = HuggingFaceEmbeddings()

db = Vald.from_documents(
    documents,
    embeddings,
    host="localhost",
    port=443,
    grpc_use_secure=True,
    grpc_credentials=credentials,
    grpc_metadata=metadata,
)
```


```python
query = "What did the president say about Ketanji Brown Jackson"
docs = db.similarity_search(query, grpc_metadata=metadata)
docs[0].page_content
```

### Similarity search by vector


```python
embedding_vector = embeddings.embed_query(query)
docs = db.similarity_search_by_vector(embedding_vector, grpc_metadata=metadata)
docs[0].page_content
```

### Similarity search with score


```python
docs_and_scores = db.similarity_search_with_score(query, grpc_metadata=metadata)
docs_and_scores[0]
```

### Maximal Marginal Relevance Search (MMR)


```python
retriever = db.as_retriever(
    search_kwargs={"search_type": "mmr", "grpc_metadata": metadata}
)
retriever.invoke(query, grpc_metadata=metadata)
```

Or:


```python
db.max_marginal_relevance_search(query, k=2, fetch_k=10, grpc_metadata=metadata)
```


## Related

- Vector store [conceptual guide](/docs/concepts/#vector-stores)
- Vector store [how-to guides](/docs/how_to/#vector-stores)