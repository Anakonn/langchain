---
canonical: https://python.langchain.com/v0.2/docs/integrations/vectorstores/typesense/
custom_edit_url: https://github.com/langchain-ai/langchain/edit/master/docs/docs/integrations/vectorstores/typesense.ipynb
---

# Typesense

> [Typesense](https://typesense.org) is an open-source, in-memory search engine, that you can either [self-host](https://typesense.org/docs/guide/install-typesense#option-2-local-machine-self-hosting) or run on [Typesense Cloud](https://cloud.typesense.org/).
>
> Typesense focuses on performance by storing the entire index in RAM (with a backup on disk) and also focuses on providing an out-of-the-box developer experience by simplifying available options and setting good defaults.
>
> It also lets you combine attribute-based filtering together with vector queries, to fetch the most relevant documents.

This notebook shows you how to use Typesense as your VectorStore.

Let's first install our dependencies:


```python
%pip install --upgrade --quiet  typesense openapi-schema-pydantic langchain-openai langchain-community tiktoken
```

We want to use `OpenAIEmbeddings` so we have to get the OpenAI API Key.


```python
import getpass
import os

os.environ["OPENAI_API_KEY"] = getpass.getpass("OpenAI API Key:")
```


```python
<!--IMPORTS:[{"imported": "TextLoader", "source": "langchain_community.document_loaders", "docs": "https://api.python.langchain.com/en/latest/document_loaders/langchain_community.document_loaders.text.TextLoader.html", "title": "Typesense"}, {"imported": "Typesense", "source": "langchain_community.vectorstores", "docs": "https://api.python.langchain.com/en/latest/vectorstores/langchain_community.vectorstores.typesense.Typesense.html", "title": "Typesense"}, {"imported": "OpenAIEmbeddings", "source": "langchain_openai", "docs": "https://api.python.langchain.com/en/latest/embeddings/langchain_openai.embeddings.base.OpenAIEmbeddings.html", "title": "Typesense"}, {"imported": "CharacterTextSplitter", "source": "langchain_text_splitters", "docs": "https://api.python.langchain.com/en/latest/character/langchain_text_splitters.character.CharacterTextSplitter.html", "title": "Typesense"}]-->
from langchain_community.document_loaders import TextLoader
from langchain_community.vectorstores import Typesense
from langchain_openai import OpenAIEmbeddings
from langchain_text_splitters import CharacterTextSplitter
```

Let's import our test dataset:


```python
loader = TextLoader("../../how_to/state_of_the_union.txt")
documents = loader.load()
text_splitter = CharacterTextSplitter(chunk_size=1000, chunk_overlap=0)
docs = text_splitter.split_documents(documents)

embeddings = OpenAIEmbeddings()
```


```python
docsearch = Typesense.from_documents(
    docs,
    embeddings,
    typesense_client_params={
        "host": "localhost",  # Use xxx.a1.typesense.net for Typesense Cloud
        "port": "8108",  # Use 443 for Typesense Cloud
        "protocol": "http",  # Use https for Typesense Cloud
        "typesense_api_key": "xyz",
        "typesense_collection_name": "lang-chain",
    },
)
```

## Similarity Search


```python
query = "What did the president say about Ketanji Brown Jackson"
found_docs = docsearch.similarity_search(query)
```


```python
print(found_docs[0].page_content)
```

## Typesense as a Retriever

Typesense, as all the other vector stores, is a LangChain Retriever, by using cosine similarity.


```python
retriever = docsearch.as_retriever()
retriever
```


```python
query = "What did the president say about Ketanji Brown Jackson"
retriever.invoke(query)[0]
```


## Related

- Vector store [conceptual guide](/docs/concepts/#vector-stores)
- Vector store [how-to guides](/docs/how_to/#vector-stores)