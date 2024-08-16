---
canonical: https://python.langchain.com/v0.2/docs/how_to/time_weighted_vectorstore/
custom_edit_url: https://github.com/langchain-ai/langchain/edit/master/docs/docs/how_to/time_weighted_vectorstore.ipynb
---

# How to use a time-weighted vector store retriever

This retriever uses a combination of semantic similarity and a time decay.

The algorithm for scoring them is:

```
semantic_similarity + (1.0 - decay_rate) ^ hours_passed
```

Notably, `hours_passed` refers to the hours passed since the object in the retriever **was last accessed**, not since it was created. This means that frequently accessed objects remain "fresh".



```python
<!--IMPORTS:[{"imported": "TimeWeightedVectorStoreRetriever", "source": "langchain.retrievers", "docs": "https://api.python.langchain.com/en/latest/retrievers/langchain.retrievers.time_weighted_retriever.TimeWeightedVectorStoreRetriever.html", "title": "How to use a time-weighted vector store retriever"}, {"imported": "InMemoryDocstore", "source": "langchain_community.docstore", "docs": "https://api.python.langchain.com/en/latest/docstore/langchain_community.docstore.in_memory.InMemoryDocstore.html", "title": "How to use a time-weighted vector store retriever"}, {"imported": "FAISS", "source": "langchain_community.vectorstores", "docs": "https://api.python.langchain.com/en/latest/vectorstores/langchain_community.vectorstores.faiss.FAISS.html", "title": "How to use a time-weighted vector store retriever"}, {"imported": "Document", "source": "langchain_core.documents", "docs": "https://api.python.langchain.com/en/latest/documents/langchain_core.documents.base.Document.html", "title": "How to use a time-weighted vector store retriever"}, {"imported": "OpenAIEmbeddings", "source": "langchain_openai", "docs": "https://api.python.langchain.com/en/latest/embeddings/langchain_openai.embeddings.base.OpenAIEmbeddings.html", "title": "How to use a time-weighted vector store retriever"}]-->
from datetime import datetime, timedelta

import faiss
from langchain.retrievers import TimeWeightedVectorStoreRetriever
from langchain_community.docstore import InMemoryDocstore
from langchain_community.vectorstores import FAISS
from langchain_core.documents import Document
from langchain_openai import OpenAIEmbeddings
```

## Low decay rate

A low `decay rate` (in this, to be extreme, we will set it close to 0) means memories will be "remembered" for longer. A `decay rate` of 0 means memories never be forgotten, making this retriever equivalent to the vector lookup.



```python
# Define your embedding model
embeddings_model = OpenAIEmbeddings()
# Initialize the vectorstore as empty
embedding_size = 1536
index = faiss.IndexFlatL2(embedding_size)
vectorstore = FAISS(embeddings_model, index, InMemoryDocstore({}), {})
retriever = TimeWeightedVectorStoreRetriever(
    vectorstore=vectorstore, decay_rate=0.0000000000000000000000001, k=1
)
```


```python
yesterday = datetime.now() - timedelta(days=1)
retriever.add_documents(
    [Document(page_content="hello world", metadata={"last_accessed_at": yesterday})]
)
retriever.add_documents([Document(page_content="hello foo")])
```



```output
['c3dcf671-3c0a-4273-9334-c4a913076bfa']
```



```python
# "Hello World" is returned first because it is most salient, and the decay rate is close to 0., meaning it's still recent enough
retriever.get_relevant_documents("hello world")
```



```output
[Document(page_content='hello world', metadata={'last_accessed_at': datetime.datetime(2023, 12, 27, 15, 30, 18, 457125), 'created_at': datetime.datetime(2023, 12, 27, 15, 30, 8, 442662), 'buffer_idx': 0})]
```


## High decay rate

With a high `decay rate` (e.g., several 9's), the `recency score` quickly goes to 0! If you set this all the way to 1, `recency` is 0 for all objects, once again making this equivalent to a vector lookup.




```python
# Define your embedding model
embeddings_model = OpenAIEmbeddings()
# Initialize the vectorstore as empty
embedding_size = 1536
index = faiss.IndexFlatL2(embedding_size)
vectorstore = FAISS(embeddings_model, index, InMemoryDocstore({}), {})
retriever = TimeWeightedVectorStoreRetriever(
    vectorstore=vectorstore, decay_rate=0.999, k=1
)
```


```python
yesterday = datetime.now() - timedelta(days=1)
retriever.add_documents(
    [Document(page_content="hello world", metadata={"last_accessed_at": yesterday})]
)
retriever.add_documents([Document(page_content="hello foo")])
```



```output
['eb1c4c86-01a8-40e3-8393-9a927295a950']
```



```python
# "Hello Foo" is returned first because "hello world" is mostly forgotten
retriever.get_relevant_documents("hello world")
```



```output
[Document(page_content='hello foo', metadata={'last_accessed_at': datetime.datetime(2023, 12, 27, 15, 30, 50, 57185), 'created_at': datetime.datetime(2023, 12, 27, 15, 30, 44, 720490), 'buffer_idx': 1})]
```


## Virtual time

Using some utils in LangChain, you can mock out the time component.



```python
<!--IMPORTS:[{"imported": "mock_now", "source": "langchain_core.utils", "docs": "https://api.python.langchain.com/en/latest/utils/langchain_core.utils.utils.mock_now.html", "title": "How to use a time-weighted vector store retriever"}]-->
import datetime

from langchain_core.utils import mock_now
```


```python
# Notice the last access time is that date time
with mock_now(datetime.datetime(2024, 2, 3, 10, 11)):
    print(retriever.get_relevant_documents("hello world"))
```
```output
[Document(page_content='hello world', metadata={'last_accessed_at': MockDateTime(2024, 2, 3, 10, 11), 'created_at': datetime.datetime(2023, 12, 27, 15, 30, 44, 532941), 'buffer_idx': 0})]
```