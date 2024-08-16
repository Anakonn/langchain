---
canonical: https://python.langchain.com/v0.2/docs/integrations/vectorstores/apache_doris/
custom_edit_url: https://github.com/langchain-ai/langchain/edit/master/docs/docs/integrations/vectorstores/apache_doris.ipynb
---

# Apache Doris

>[Apache Doris](https://doris.apache.org/) is a modern data warehouse for real-time analytics.
It delivers lightning-fast analytics on real-time data at scale.

>Usually `Apache Doris` is categorized into OLAP, and it has showed excellent performance in [ClickBench — a Benchmark For Analytical DBMS](https://benchmark.clickhouse.com/). Since it has a super-fast vectorized execution engine, it could also be used as a fast vectordb.

You'll need to install `langchain-community` with `pip install -qU langchain-community` to use this integration

Here we'll show how to use the Apache Doris Vector Store.

## Setup


```python
%pip install --upgrade --quiet  pymysql
```

Set `update_vectordb = False` at the beginning. If there is no docs updated, then we don't need to rebuild the embeddings of docs


```python
!pip install  sqlalchemy
!pip install langchain
```


```python
<!--IMPORTS:[{"imported": "RetrievalQA", "source": "langchain.chains", "docs": "https://api.python.langchain.com/en/latest/chains/langchain.chains.retrieval_qa.base.RetrievalQA.html", "title": "Apache Doris"}, {"imported": "DirectoryLoader", "source": "langchain_community.document_loaders", "docs": "https://api.python.langchain.com/en/latest/document_loaders/langchain_community.document_loaders.directory.DirectoryLoader.html", "title": "Apache Doris"}, {"imported": "UnstructuredMarkdownLoader", "source": "langchain_community.document_loaders", "docs": "https://api.python.langchain.com/en/latest/document_loaders/langchain_community.document_loaders.markdown.UnstructuredMarkdownLoader.html", "title": "Apache Doris"}, {"imported": "ApacheDoris", "source": "langchain_community.vectorstores.apache_doris", "docs": "https://api.python.langchain.com/en/latest/vectorstores/langchain_community.vectorstores.apache_doris.ApacheDoris.html", "title": "Apache Doris"}, {"imported": "ApacheDorisSettings", "source": "langchain_community.vectorstores.apache_doris", "docs": "https://api.python.langchain.com/en/latest/vectorstores/langchain_community.vectorstores.apache_doris.ApacheDorisSettings.html", "title": "Apache Doris"}, {"imported": "OpenAI", "source": "langchain_openai", "docs": "https://api.python.langchain.com/en/latest/llms/langchain_openai.llms.base.OpenAI.html", "title": "Apache Doris"}, {"imported": "OpenAIEmbeddings", "source": "langchain_openai", "docs": "https://api.python.langchain.com/en/latest/embeddings/langchain_openai.embeddings.base.OpenAIEmbeddings.html", "title": "Apache Doris"}, {"imported": "TokenTextSplitter", "source": "langchain_text_splitters", "docs": "https://api.python.langchain.com/en/latest/base/langchain_text_splitters.base.TokenTextSplitter.html", "title": "Apache Doris"}]-->
from langchain.chains import RetrievalQA
from langchain_community.document_loaders import (
    DirectoryLoader,
    UnstructuredMarkdownLoader,
)
from langchain_community.vectorstores.apache_doris import (
    ApacheDoris,
    ApacheDorisSettings,
)
from langchain_openai import OpenAI, OpenAIEmbeddings
from langchain_text_splitters import TokenTextSplitter

update_vectordb = False
```

## Load docs and split them into tokens

Load all markdown files under the `docs` directory

for Apache Doris documents, you can clone repo from https://github.com/apache/doris, and there is `docs` directory in it.


```python
loader = DirectoryLoader(
    "./docs", glob="**/*.md", loader_cls=UnstructuredMarkdownLoader
)
documents = loader.load()
```

Split docs into tokens, and set `update_vectordb = True` because there are new docs/tokens.


```python
# load text splitter and split docs into snippets of text
text_splitter = TokenTextSplitter(chunk_size=400, chunk_overlap=50)
split_docs = text_splitter.split_documents(documents)

# tell vectordb to update text embeddings
update_vectordb = True
```

split_docs[-20]

print("# docs  = %d, # splits = %d" % (len(documents), len(split_docs)))

## Create vectordb instance

### Use Apache Doris as vectordb


```python
def gen_apache_doris(update_vectordb, embeddings, settings):
    if update_vectordb:
        docsearch = ApacheDoris.from_documents(split_docs, embeddings, config=settings)
    else:
        docsearch = ApacheDoris(embeddings, settings)
    return docsearch
```

## Convert tokens into embeddings and put them into vectordb

Here we use Apache Doris as vectordb, you can configure Apache Doris instance via `ApacheDorisSettings`.

Configuring Apache Doris instance is pretty much like configuring mysql instance. You need to specify:
1. host/port
2. username(default: 'root')
3. password(default: '')
4. database(default: 'default')
5. table(default: 'langchain')


```python
import os
from getpass import getpass

os.environ["OPENAI_API_KEY"] = getpass()
```


```python
update_vectordb = True

embeddings = OpenAIEmbeddings()

# configure Apache Doris settings(host/port/user/pw/db)
settings = ApacheDorisSettings()
settings.port = 9030
settings.host = "172.30.34.130"
settings.username = "root"
settings.password = ""
settings.database = "langchain"
docsearch = gen_apache_doris(update_vectordb, embeddings, settings)

print(docsearch)

update_vectordb = False
```

## Build QA and ask question to it


```python
llm = OpenAI()
qa = RetrievalQA.from_chain_type(
    llm=llm, chain_type="stuff", retriever=docsearch.as_retriever()
)
query = "what is apache doris"
resp = qa.run(query)
print(resp)
```


## Related

- Vector store [conceptual guide](/docs/concepts/#vector-stores)
- Vector store [how-to guides](/docs/how_to/#vector-stores)