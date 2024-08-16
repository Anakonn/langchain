---
canonical: https://python.langchain.com/v0.2/docs/integrations/vectorstores/pinecone/
custom_edit_url: https://github.com/langchain-ai/langchain/edit/master/docs/docs/integrations/vectorstores/pinecone.ipynb
---

# Pinecone

>[Pinecone](https://docs.pinecone.io/docs/overview) is a vector database with broad functionality.

This notebook shows how to use functionality related to the `Pinecone` vector database.

## Setup

To use the `PineconeVectorStore` you first need to install the partner package, as well as the other packages used throughout this notebook.


```python
%pip install -qU langchain-pinecone pinecone-notebooks
```

Migration note: if you are migrating from the `langchain_community.vectorstores` implementation of Pinecone, you may need to remove your `pinecone-client` v2 dependency before installing `langchain-pinecone`, which relies on `pinecone-client` v3.

### Credentials

Create a new Pinecone account, or sign into your existing one, and create an API key to use in this notebook.


```python
import getpass
import os
import time

from pinecone import Pinecone, ServerlessSpec

if not os.getenv("PINECONE_API_KEY"):
    os.environ["PINECONE_API_KEY"] = getpass.getpass("Enter your Pinecone API key: ")

pinecone_api_key = os.environ.get("PINECONE_API_KEY")

pc = Pinecone(api_key=pinecone_api_key)
```

If you want to get automated tracing of your model calls you can also set your [LangSmith](https://docs.smith.langchain.com/) API key by uncommenting below:


```python
# os.environ["LANGSMITH_API_KEY"] = getpass.getpass("Enter your LangSmith API key: ")
# os.environ["LANGSMITH_TRACING"] = "true"
```

## Initialization

Before initializing our vector store, let's connect to a Pinecone index. If one named `index_name` doesn't exist, it will be created.


```python
import time

index_name = "langchain-test-index"  # change if desired

existing_indexes = [index_info["name"] for index_info in pc.list_indexes()]

if index_name not in existing_indexes:
    pc.create_index(
        name=index_name,
        dimension=3072,
        metric="cosine",
        spec=ServerlessSpec(cloud="aws", region="us-east-1"),
    )
    while not pc.describe_index(index_name).status["ready"]:
        time.sleep(1)

index = pc.Index(index_name)
```

Now that our Pinecone index is setup, we can initialize our vector store. 

import EmbeddingTabs from "@theme/EmbeddingTabs";

<EmbeddingTabs/>


```python
<!--IMPORTS:[{"imported": "PineconeVectorStore", "source": "langchain_pinecone", "docs": "https://api.python.langchain.com/en/latest/vectorstores/langchain_pinecone.vectorstores.PineconeVectorStore.html", "title": "Pinecone"}]-->
from langchain_pinecone import PineconeVectorStore

vector_store = PineconeVectorStore(index=index, embedding=embeddings)
```

## Manage vector store

Once you have created your vector store, we can interact with it by adding and deleting different items.

### Add items to vector store

We can add items to our vector store by using the `add_documents` function.


```python
<!--IMPORTS:[{"imported": "Document", "source": "langchain_core.documents", "docs": "https://api.python.langchain.com/en/latest/documents/langchain_core.documents.base.Document.html", "title": "Pinecone"}]-->
from uuid import uuid4

from langchain_core.documents import Document

document_1 = Document(
    page_content="I had chocalate chip pancakes and scrambled eggs for breakfast this morning.",
    metadata={"source": "tweet"},
)

document_2 = Document(
    page_content="The weather forecast for tomorrow is cloudy and overcast, with a high of 62 degrees.",
    metadata={"source": "news"},
)

document_3 = Document(
    page_content="Building an exciting new project with LangChain - come check it out!",
    metadata={"source": "tweet"},
)

document_4 = Document(
    page_content="Robbers broke into the city bank and stole $1 million in cash.",
    metadata={"source": "news"},
)

document_5 = Document(
    page_content="Wow! That was an amazing movie. I can't wait to see it again.",
    metadata={"source": "tweet"},
)

document_6 = Document(
    page_content="Is the new iPhone worth the price? Read this review to find out.",
    metadata={"source": "website"},
)

document_7 = Document(
    page_content="The top 10 soccer players in the world right now.",
    metadata={"source": "website"},
)

document_8 = Document(
    page_content="LangGraph is the best framework for building stateful, agentic applications!",
    metadata={"source": "tweet"},
)

document_9 = Document(
    page_content="The stock market is down 500 points today due to fears of a recession.",
    metadata={"source": "news"},
)

document_10 = Document(
    page_content="I have a bad feeling I am going to get deleted :(",
    metadata={"source": "tweet"},
)

documents = [
    document_1,
    document_2,
    document_3,
    document_4,
    document_5,
    document_6,
    document_7,
    document_8,
    document_9,
    document_10,
]
uuids = [str(uuid4()) for _ in range(len(documents))]

vector_store.add_documents(documents=documents, ids=uuids)
```



```output
['167b8681-5974-467f-adcb-6e987a18df01',
 'd16010fd-41f8-4d49-9c22-c66d5555a3fe',
 'ffcacfb3-2bc2-44c3-a039-c2256a905c0e',
 'cf3bfc9f-5dc7-4f5e-bb41-edb957394126',
 'e99b07eb-fdff-4cb9-baa8-619fd8efeed3',
 '68c93033-a24f-40bd-8492-92fa26b631a4',
 'b27a4ecb-b505-4c5d-89ff-526e3d103558',
 '4868a9e6-e6fb-4079-b400-4a1dfbf0d4c4',
 '921c0e9c-0550-4eb5-9a6c-ed44410788b2',
 'c446fc23-64e8-47e7-8c19-ecf985e9411e']
```


### Delete items from vector store


```python
vector_store.delete(ids=[uuids[-1]])
```

## Query vector store

Once your vector store has been created and the relevant documents have been added you will most likely wish to query it during the running of your chain or agent. 

### Query directly

Performing a simple similarity search can be done as follows:


```python
results = vector_store.similarity_search(
    "LangChain provides abstractions to make working with LLMs easy",
    k=2,
    filter={"source": "tweet"},
)
for res in results:
    print(f"* {res.page_content} [{res.metadata}]")
```
```output
* Building an exciting new project with LangChain - come check it out! [{'source': 'tweet'}]
* LangGraph is the best framework for building stateful, agentic applications! [{'source': 'tweet'}]
```
#### Similarity search with score

You can also search with score:


```python
results = vector_store.similarity_search_with_score(
    "Will it be hot tomorrow?", k=1, filter={"source": "news"}
)
for res, score in results:
    print(f"* [SIM={score:3f}] {res.page_content} [{res.metadata}]")
```
```output
* [SIM=0.553187] The weather forecast for tomorrow is cloudy and overcast, with a high of 62 degrees. [{'source': 'news'}]
```
#### Other search methods

There are more search methods (such as MMR) not listed in this notebook, to find all of them be sure to read the [API reference](https://api.python.langchain.com/en/latest/vectorstores/langchain_pinecone.vectorstores.PineconeVectorStore.html).

### Query by turning into retriever

You can also transform the vector store into a retriever for easier usage in your chains.


```python
retriever = vector_store.as_retriever(
    search_type="similarity_score_threshold",
    search_kwargs={"k": 1, "score_threshold": 0.5},
)
retriever.invoke("Stealing from the bank is a crime", filter={"source": "news"})
```



```output
[Document(metadata={'source': 'news'}, page_content='Robbers broke into the city bank and stole $1 million in cash.')]
```


## Usage for retrieval-augmented generation

For guides on how to use this vector store for retrieval-augmented generation (RAG), see the following sections:

- [Tutorials: working with external knowledge](https://python.langchain.com/v0.2/docs/tutorials/#working-with-external-knowledge)
- [How-to: Question and answer with RAG](https://python.langchain.com/v0.2/docs/how_to/#qa-with-rag)
- [Retrieval conceptual docs](https://python.langchain.com/v0.2/docs/concepts/#retrieval)

## API reference

For detailed documentation of all __ModuleName__VectorStore features and configurations head to the API reference: https://api.python.langchain.com/en/latest/vectorstores/langchain_pinecone.vectorstores.PineconeVectorStore.html


## Related

- Vector store [conceptual guide](/docs/concepts/#vector-stores)
- Vector store [how-to guides](/docs/how_to/#vector-stores)