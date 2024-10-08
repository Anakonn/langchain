---
canonical: https://python.langchain.com/v0.2/docs/integrations/vectorstores/semadb/
custom_edit_url: https://github.com/langchain-ai/langchain/edit/master/docs/docs/integrations/vectorstores/semadb.ipynb
---

# SemaDB

> [SemaDB](https://www.semafind.com/products/semadb) from [SemaFind](https://www.semafind.com) is a no fuss vector similarity database for building AI applications. The hosted `SemaDB Cloud` offers a no fuss developer experience to get started.

The full documentation of the API along with examples and an interactive playground is available on [RapidAPI](https://rapidapi.com/semafind-semadb/api/semadb).

This notebook demonstrates usage of the `SemaDB Cloud` vector store.

You'll need to install `langchain-community` with `pip install -qU langchain-community` to use this integration

## Load document embeddings

To run things locally, we are using [Sentence Transformers](https://www.sbert.net/) which are commonly used for embedding sentences. You can use any embedding model LangChain offers.


```python
%pip install --upgrade --quiet  sentence_transformers
```


```python
<!--IMPORTS:[{"imported": "HuggingFaceEmbeddings", "source": "langchain_huggingface", "docs": "https://api.python.langchain.com/en/latest/embeddings/langchain_huggingface.embeddings.huggingface.HuggingFaceEmbeddings.html", "title": "SemaDB"}]-->
from langchain_huggingface import HuggingFaceEmbeddings

embeddings = HuggingFaceEmbeddings()
```


```python
<!--IMPORTS:[{"imported": "TextLoader", "source": "langchain_community.document_loaders", "docs": "https://api.python.langchain.com/en/latest/document_loaders/langchain_community.document_loaders.text.TextLoader.html", "title": "SemaDB"}, {"imported": "CharacterTextSplitter", "source": "langchain_text_splitters", "docs": "https://api.python.langchain.com/en/latest/character/langchain_text_splitters.character.CharacterTextSplitter.html", "title": "SemaDB"}]-->
from langchain_community.document_loaders import TextLoader
from langchain_text_splitters import CharacterTextSplitter

loader = TextLoader("../../how_to/state_of_the_union.txt")
documents = loader.load()
text_splitter = CharacterTextSplitter(chunk_size=400, chunk_overlap=0)
docs = text_splitter.split_documents(documents)
print(len(docs))
```
```output
114
```
## Connect to SemaDB

SemaDB Cloud uses [RapidAPI keys](https://rapidapi.com/semafind-semadb/api/semadb) to authenticate. You can obtain yours by creating a free RapidAPI account.


```python
import getpass
import os

os.environ["SEMADB_API_KEY"] = getpass.getpass("SemaDB API Key:")
```
```output
SemaDB API Key: ········
```

```python
<!--IMPORTS:[{"imported": "SemaDB", "source": "langchain_community.vectorstores", "docs": "https://api.python.langchain.com/en/latest/vectorstores/langchain_community.vectorstores.semadb.SemaDB.html", "title": "SemaDB"}, {"imported": "DistanceStrategy", "source": "langchain_community.vectorstores.utils", "docs": "https://api.python.langchain.com/en/latest/vectorstores/langchain_community.vectorstores.utils.DistanceStrategy.html", "title": "SemaDB"}]-->
from langchain_community.vectorstores import SemaDB
from langchain_community.vectorstores.utils import DistanceStrategy
```

The parameters to the SemaDB vector store reflect the API directly:

- "mycollection": is the collection name in which we will store these vectors.
- 768: is dimensions of the vectors. In our case, the sentence transformer embeddings yield 768 dimensional vectors.
- API_KEY: is your RapidAPI key.
- embeddings: correspond to how the embeddings of documents, texts and queries will be generated.
- DistanceStrategy: is the distance metric used. The wrapper automatically normalises vectors if COSINE is used.


```python
db = SemaDB("mycollection", 768, embeddings, DistanceStrategy.COSINE)

# Create collection if running for the first time. If the collection
# already exists this will fail.
db.create_collection()
```



```output
True
```


The SemaDB vector store wrapper adds the document text as point metadata to collect later. Storing large chunks of text is *not recommended*. If you are indexing a large collection, we instead recommend storing references to the documents such as external Ids.


```python
db.add_documents(docs)[:2]
```



```output
['813c7ef3-9797-466b-8afa-587115592c6c',
 'fc392f7f-082b-4932-bfcc-06800db5e017']
```


## Similarity Search

We use the default LangChain similarity search interface to search for the most similar sentences.


```python
query = "What did the president say about Ketanji Brown Jackson"
docs = db.similarity_search(query)
print(docs[0].page_content)
```
```output
And I did that 4 days ago, when I nominated Circuit Court of Appeals Judge Ketanji Brown Jackson. One of our nation’s top legal minds, who will continue Justice Breyer’s legacy of excellence.
```

```python
docs = db.similarity_search_with_score(query)
docs[0]
```



```output
(Document(page_content='And I did that 4 days ago, when I nominated Circuit Court of Appeals Judge Ketanji Brown Jackson. One of our nation’s top legal minds, who will continue Justice Breyer’s legacy of excellence.', metadata={'source': '../../how_to/state_of_the_union.txt', 'text': 'And I did that 4 days ago, when I nominated Circuit Court of Appeals Judge Ketanji Brown Jackson. One of our nation’s top legal minds, who will continue Justice Breyer’s legacy of excellence.'}),
 0.42369342)
```


## Clean up

You can delete the collection to remove all data.


```python
db.delete_collection()
```



```output
True
```



## Related

- Vector store [conceptual guide](/docs/concepts/#vector-stores)
- Vector store [how-to guides](/docs/how_to/#vector-stores)