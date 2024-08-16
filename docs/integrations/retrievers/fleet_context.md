---
canonical: https://python.langchain.com/v0.2/docs/integrations/retrievers/fleet_context/
custom_edit_url: https://github.com/langchain-ai/langchain/edit/master/docs/docs/integrations/retrievers/fleet_context.ipynb
---

# Fleet AI Context

>[Fleet AI Context](https://www.fleet.so/context) is a dataset of high-quality embeddings of the top 1200 most popular & permissive Python Libraries & their documentation.
>
>The `Fleet AI` team is on a mission to embed the world's most important data. They've started by embedding the top 1200 Python libraries to enable code generation with up-to-date knowledge. They've been kind enough to share their embeddings of the [LangChain docs](/docs/introduction) and [API reference](https://api.python.langchain.com/en/latest/api_reference.html).

Let's take a look at how we can use these embeddings to power a docs retrieval system and ultimately a simple code-generating chain!


```python
%pip install --upgrade --quiet  langchain fleet-context langchain-openai pandas faiss-cpu # faiss-gpu for CUDA supported GPU
```


```python
<!--IMPORTS:[{"imported": "MultiVectorRetriever", "source": "langchain.retrievers", "docs": "https://api.python.langchain.com/en/latest/retrievers/langchain.retrievers.multi_vector.MultiVectorRetriever.html", "title": "Fleet AI Context"}, {"imported": "FAISS", "source": "langchain_community.vectorstores", "docs": "https://api.python.langchain.com/en/latest/vectorstores/langchain_community.vectorstores.faiss.FAISS.html", "title": "Fleet AI Context"}, {"imported": "Document", "source": "langchain_core.documents", "docs": "https://api.python.langchain.com/en/latest/documents/langchain_core.documents.base.Document.html", "title": "Fleet AI Context"}, {"imported": "BaseStore", "source": "langchain_core.stores", "docs": "https://api.python.langchain.com/en/latest/stores/langchain_core.stores.BaseStore.html", "title": "Fleet AI Context"}, {"imported": "VectorStore", "source": "langchain_core.vectorstores", "docs": "https://api.python.langchain.com/en/latest/vectorstores/langchain_core.vectorstores.base.VectorStore.html", "title": "Fleet AI Context"}, {"imported": "OpenAIEmbeddings", "source": "langchain_openai", "docs": "https://api.python.langchain.com/en/latest/embeddings/langchain_openai.embeddings.base.OpenAIEmbeddings.html", "title": "Fleet AI Context"}]-->
from operator import itemgetter
from typing import Any, Optional, Type

import pandas as pd
from langchain.retrievers import MultiVectorRetriever
from langchain_community.vectorstores import FAISS
from langchain_core.documents import Document
from langchain_core.stores import BaseStore
from langchain_core.vectorstores import VectorStore
from langchain_openai import OpenAIEmbeddings


def load_fleet_retriever(
    df: pd.DataFrame,
    *,
    vectorstore_cls: Type[VectorStore] = FAISS,
    docstore: Optional[BaseStore] = None,
    **kwargs: Any,
):
    vectorstore = _populate_vectorstore(df, vectorstore_cls)
    if docstore is None:
        return vectorstore.as_retriever(**kwargs)
    else:
        _populate_docstore(df, docstore)
        return MultiVectorRetriever(
            vectorstore=vectorstore, docstore=docstore, id_key="parent", **kwargs
        )


def _populate_vectorstore(
    df: pd.DataFrame,
    vectorstore_cls: Type[VectorStore],
) -> VectorStore:
    if not hasattr(vectorstore_cls, "from_embeddings"):
        raise ValueError(
            f"Incompatible vector store class {vectorstore_cls}."
            "Must implement `from_embeddings` class method."
        )
    texts_embeddings = []
    metadatas = []
    for _, row in df.iterrows():
        texts_embeddings.append((row.metadata["text"], row["dense_embeddings"]))
        metadatas.append(row.metadata)
    return vectorstore_cls.from_embeddings(
        texts_embeddings,
        OpenAIEmbeddings(model="text-embedding-ada-002"),
        metadatas=metadatas,
    )


def _populate_docstore(df: pd.DataFrame, docstore: BaseStore) -> None:
    parent_docs = []
    df = df.copy()
    df["parent"] = df.metadata.apply(itemgetter("parent"))
    for parent_id, group in df.groupby("parent"):
        sorted_group = group.iloc[
            group.metadata.apply(itemgetter("section_index")).argsort()
        ]
        text = "".join(sorted_group.metadata.apply(itemgetter("text")))
        metadata = {
            k: sorted_group.iloc[0].metadata[k] for k in ("title", "type", "url")
        }
        text = metadata["title"] + "\n" + text
        metadata["id"] = parent_id
        parent_docs.append(Document(page_content=text, metadata=metadata))
    docstore.mset(((d.metadata["id"], d) for d in parent_docs))
```

## Retriever chunks

As part of their embedding process, the Fleet AI team first chunked long documents before embedding them. This means the vectors correspond to sections of pages in the LangChain docs, not entire pages. By default, when we spin up a retriever from these embeddings, we'll be retrieving these embedded chunks.

We will be using Fleet Context's `download_embeddings()` to grab Langchain's documentation embeddings. You can view all supported libraries' documentation at https://fleet.so/context.


```python
from context import download_embeddings

df = download_embeddings("langchain")
vecstore_retriever = load_fleet_retriever(df)
```


```python
vecstore_retriever.invoke("How does the multi vector retriever work")
```

## Other packages

You can download and use other embeddings from [this Dropbox link](https://www.dropbox.com/scl/fo/54t2e7fogtixo58pnlyub/h?rlkey=tne16wkssgf01jor0p1iqg6p9&dl=0).

## Retrieve parent docs

The embeddings provided by Fleet AI contain metadata that indicates which embedding chunks correspond to the same original document page. If we'd like we can use this information to retrieve whole parent documents, and not just embedded chunks. Under the hood, we'll use a MultiVectorRetriever and a BaseStore object to search for relevant chunks and then map them to their parent document.


```python
<!--IMPORTS:[{"imported": "InMemoryStore", "source": "langchain.storage", "docs": "https://api.python.langchain.com/en/latest/stores/langchain_core.stores.InMemoryStore.html", "title": "Fleet AI Context"}]-->
from langchain.storage import InMemoryStore

parent_retriever = load_fleet_retriever(
    "https://www.dropbox.com/scl/fi/4rescpkrg9970s3huz47l/libraries_langchain_release.parquet?rlkey=283knw4wamezfwiidgpgptkep&dl=1",
    docstore=InMemoryStore(),
)
```


```python
parent_retriever.invoke("How does the multi vector retriever work")
```

## Putting it in a chain

Let's try using our retrieval systems in a simple chain!


```python
<!--IMPORTS:[{"imported": "StrOutputParser", "source": "langchain_core.output_parsers", "docs": "https://api.python.langchain.com/en/latest/output_parsers/langchain_core.output_parsers.string.StrOutputParser.html", "title": "Fleet AI Context"}, {"imported": "ChatPromptTemplate", "source": "langchain_core.prompts", "docs": "https://api.python.langchain.com/en/latest/prompts/langchain_core.prompts.chat.ChatPromptTemplate.html", "title": "Fleet AI Context"}, {"imported": "RunnablePassthrough", "source": "langchain_core.runnables", "docs": "https://api.python.langchain.com/en/latest/runnables/langchain_core.runnables.passthrough.RunnablePassthrough.html", "title": "Fleet AI Context"}, {"imported": "ChatOpenAI", "source": "langchain_openai", "docs": "https://api.python.langchain.com/en/latest/chat_models/langchain_openai.chat_models.base.ChatOpenAI.html", "title": "Fleet AI Context"}]-->
from langchain_core.output_parsers import StrOutputParser
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.runnables import RunnablePassthrough
from langchain_openai import ChatOpenAI

prompt = ChatPromptTemplate.from_messages(
    [
        (
            "system",
            """You are a great software engineer who is very familiar \
with Python. Given a user question or request about a new Python library called LangChain and \
parts of the LangChain documentation, answer the question or generate the requested code. \
Your answers must be accurate, should include code whenever possible, and should assume anything \
about LangChain which is note explicitly stated in the LangChain documentation. If the required \
information is not available, just say so.

LangChain Documentation
------------------

{context}""",
        ),
        ("human", "{question}"),
    ]
)

model = ChatOpenAI(model="gpt-3.5-turbo-16k")

chain = (
    {
        "question": RunnablePassthrough(),
        "context": parent_retriever
        | (lambda docs: "\n\n".join(d.page_content for d in docs)),
    }
    | prompt
    | model
    | StrOutputParser()
)
```


```python
for chunk in chain.invoke(
    "How do I create a FAISS vector store retriever that returns 10 documents per search query"
):
    print(chunk, end="", flush=True)
```


## Related

- Retriever [conceptual guide](/docs/concepts/#retrievers)
- Retriever [how-to guides](/docs/how_to/#retrievers)