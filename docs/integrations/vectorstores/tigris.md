---
canonical: https://python.langchain.com/v0.2/docs/integrations/vectorstores/tigris/
custom_edit_url: https://github.com/langchain-ai/langchain/edit/master/docs/docs/integrations/vectorstores/tigris.ipynb
---

# Tigris

> [Tigris](https://tigrisdata.com) is an open-source Serverless NoSQL Database and Search Platform designed to simplify building high-performance vector search applications.
> `Tigris` eliminates the infrastructure complexity of managing, operating, and synchronizing multiple tools, allowing you to focus on building great applications instead.

This notebook guides you how to use Tigris as your VectorStore

**Pre requisites**
1. An OpenAI account. You can sign up for an account [here](https://platform.openai.com/)
2. [Sign up for a free Tigris account](https://console.preview.tigrisdata.cloud). Once you have signed up for the Tigris account, create a new project called `vectordemo`. Next, make a note of the *Uri* for the region you've created your project in, the **clientId** and **clientSecret**. You can get all this information from the **Application Keys** section of the project.

Let's first install our dependencies:


```python
%pip install --upgrade --quiet  tigrisdb openapi-schema-pydantic langchain-openai langchain-community tiktoken
```

We will load the `OpenAI` api key and `Tigris` credentials in our environment


```python
import getpass
import os

os.environ["OPENAI_API_KEY"] = getpass.getpass("OpenAI API Key:")
os.environ["TIGRIS_PROJECT"] = getpass.getpass("Tigris Project Name:")
os.environ["TIGRIS_CLIENT_ID"] = getpass.getpass("Tigris Client Id:")
os.environ["TIGRIS_CLIENT_SECRET"] = getpass.getpass("Tigris Client Secret:")
```


```python
<!--IMPORTS:[{"imported": "TextLoader", "source": "langchain_community.document_loaders", "docs": "https://api.python.langchain.com/en/latest/document_loaders/langchain_community.document_loaders.text.TextLoader.html", "title": "Tigris"}, {"imported": "Tigris", "source": "langchain_community.vectorstores", "docs": "https://api.python.langchain.com/en/latest/vectorstores/langchain_community.vectorstores.tigris.Tigris.html", "title": "Tigris"}, {"imported": "OpenAIEmbeddings", "source": "langchain_openai", "docs": "https://api.python.langchain.com/en/latest/embeddings/langchain_openai.embeddings.base.OpenAIEmbeddings.html", "title": "Tigris"}, {"imported": "CharacterTextSplitter", "source": "langchain_text_splitters", "docs": "https://api.python.langchain.com/en/latest/character/langchain_text_splitters.character.CharacterTextSplitter.html", "title": "Tigris"}]-->
from langchain_community.document_loaders import TextLoader
from langchain_community.vectorstores import Tigris
from langchain_openai import OpenAIEmbeddings
from langchain_text_splitters import CharacterTextSplitter
```

### Initialize Tigris vector store
Let's import our test dataset:


```python
loader = TextLoader("../../../state_of_the_union.txt")
documents = loader.load()
text_splitter = CharacterTextSplitter(chunk_size=1000, chunk_overlap=0)
docs = text_splitter.split_documents(documents)

embeddings = OpenAIEmbeddings()
```


```python
vector_store = Tigris.from_documents(docs, embeddings, index_name="my_embeddings")
```

### Similarity Search


```python
query = "What did the president say about Ketanji Brown Jackson"
found_docs = vector_store.similarity_search(query)
print(found_docs)
```

### Similarity Search with score (vector distance)


```python
query = "What did the president say about Ketanji Brown Jackson"
result = vector_store.similarity_search_with_score(query)
for doc, score in result:
    print(f"document={doc}, score={score}")
```


## Related

- Vector store [conceptual guide](/docs/concepts/#vector-stores)
- Vector store [how-to guides](/docs/how_to/#vector-stores)