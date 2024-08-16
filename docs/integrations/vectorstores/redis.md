---
canonical: https://python.langchain.com/v0.2/docs/integrations/vectorstores/redis/
custom_edit_url: https://github.com/langchain-ai/langchain/edit/master/docs/docs/integrations/vectorstores/redis.ipynb
---

# Redis

>[Redis vector database](https://redis.io/docs/get-started/vector-database/) introduction and langchain integration guide.

## What is Redis?

Most developers from a web services background are familiar with `Redis`. At its core, `Redis` is an open-source key-value store that is used as a cache, message broker, and database. Developers choose `Redis` because it is fast, has a large ecosystem of client libraries, and has been deployed by major enterprises for years.

On top of these traditional use cases, `Redis` provides additional capabilities like the Search and Query capability that allows users to create secondary index structures within `Redis`. This allows `Redis` to be a Vector Database, at the speed of a cache. 


## Redis as a Vector Database

`Redis` uses compressed, inverted indexes for fast indexing with a low memory footprint. It also supports a number of advanced features such as:

* Indexing of multiple fields in Redis hashes and `JSON`
* Vector similarity search (with `HNSW` (ANN) or `FLAT` (KNN))
* Vector Range Search (e.g. find all vectors within a radius of a query vector)
* Incremental indexing without performance loss
* Document ranking (using [tf-idf](https://en.wikipedia.org/wiki/Tf%E2%80%93idf), with optional user-provided weights)
* Field weighting
* Complex boolean queries with `AND`, `OR`, and `NOT` operators
* Prefix matching, fuzzy matching, and exact-phrase queries
* Support for [double-metaphone phonetic matching](https://redis.io/docs/stack/search/reference/phonetic_matching/)
* Auto-complete suggestions (with fuzzy prefix suggestions)
* Stemming-based query expansion in [many languages](https://redis.io/docs/stack/search/reference/stemming/) (using [Snowball](http://snowballstem.org/))
* Support for Chinese-language tokenization and querying (using [Friso](https://github.com/lionsoul2014/friso))
* Numeric filters and ranges
* Geospatial searches using Redis geospatial indexing
* A powerful aggregations engine
* Supports for all `utf-8` encoded text
* Retrieve full documents, selected fields, or only the document IDs
* Sorting results (for example, by creation date)



## Clients

Since `Redis` is much more than just a vector database, there are often use cases that demand the usage of a `Redis` client besides just the `LangChain` integration. You can use any standard `Redis` client library to run Search and Query commands, but it's easiest to use a library that wraps the Search and Query API. Below are a few examples, but you can find more client libraries [here](https://redis.io/resources/clients/).

| Project | Language | License | Author | Stars |
|----------|---------|--------|---------|-------|
| [jedis][jedis-url] | Java | MIT | [Redis][redis-url] | ![Stars][jedis-stars] |
| [redisvl][redisvl-url] | Python | MIT | [Redis][redis-url] | ![Stars][redisvl-stars] |
| [redis-py][redis-py-url] | Python | MIT | [Redis][redis-url] | ![Stars][redis-py-stars] |
| [node-redis][node-redis-url] | Node.js | MIT | [Redis][redis-url] | ![Stars][node-redis-stars] |
| [nredisstack][nredisstack-url] | .NET | MIT | [Redis][redis-url] | ![Stars][nredisstack-stars] |

[redis-url]: https://redis.com

[redisvl-url]: https://github.com/RedisVentures/redisvl
[redisvl-stars]: https://img.shields.io/github/stars/RedisVentures/redisvl.svg?style=social&amp;label=Star&amp;maxAge=2592000
[redisvl-package]: https://pypi.python.org/pypi/redisvl

[redis-py-url]: https://github.com/redis/redis-py
[redis-py-stars]: https://img.shields.io/github/stars/redis/redis-py.svg?style=social&amp;label=Star&amp;maxAge=2592000
[redis-py-package]: https://pypi.python.org/pypi/redis

[jedis-url]: https://github.com/redis/jedis
[jedis-stars]: https://img.shields.io/github/stars/redis/jedis.svg?style=social&amp;label=Star&amp;maxAge=2592000
[Jedis-package]: https://search.maven.org/artifact/redis.clients/jedis

[nredisstack-url]: https://github.com/redis/nredisstack
[nredisstack-stars]: https://img.shields.io/github/stars/redis/nredisstack.svg?style=social&amp;label=Star&amp;maxAge=2592000
[nredisstack-package]: https://www.nuget.org/packages/nredisstack/

[node-redis-url]: https://github.com/redis/node-redis
[node-redis-stars]: https://img.shields.io/github/stars/redis/node-redis.svg?style=social&amp;label=Star&amp;maxAge=2592000
[node-redis-package]: https://www.npmjs.com/package/redis

[redis-om-python-url]: https://github.com/redis/redis-om-python
[redis-om-python-author]: https://redis.com
[redis-om-python-stars]: https://img.shields.io/github/stars/redis/redis-om-python.svg?style=social&amp;label=Star&amp;maxAge=2592000

[redisearch-go-url]: https://github.com/RediSearch/redisearch-go
[redisearch-go-author]: https://redis.com
[redisearch-go-stars]: https://img.shields.io/github/stars/RediSearch/redisearch-go.svg?style=social&amp;label=Star&amp;maxAge=2592000

[redisearch-api-rs-url]: https://github.com/RediSearch/redisearch-api-rs
[redisearch-api-rs-author]: https://redis.com
[redisearch-api-rs-stars]: https://img.shields.io/github/stars/RediSearch/redisearch-api-rs.svg?style=social&amp;label=Star&amp;maxAge=2592000


## Deployment options

There are many ways to deploy Redis with RediSearch. The easiest way to get started is to use Docker, but there are are many potential options for deployment such as

- [Redis Cloud](https://redis.com/redis-enterprise-cloud/overview/)
- [Docker (Redis Stack)](https://hub.docker.com/r/redis/redis-stack)
- Cloud marketplaces: [AWS Marketplace](https://aws.amazon.com/marketplace/pp/prodview-e6y7ork67pjwg?sr=0-2&ref_=beagle&applicationId=AWSMPContessa), [Google Marketplace](https://console.cloud.google.com/marketplace/details/redislabs-public/redis-enterprise?pli=1), or [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/garantiadata.redis_enterprise_1sp_public_preview?tab=Overview)
- On-premise: [Redis Enterprise Software](https://redis.com/redis-enterprise-software/overview/)
- Kubernetes: [Redis Enterprise Software on Kubernetes](https://docs.redis.com/latest/kubernetes/)


## Additional examples

Many examples can be found in the [Redis AI team's GitHub](https://github.com/RedisVentures/)

- [Awesome Redis AI Resources](https://github.com/RedisVentures/redis-ai-resources) - List of examples of using Redis in AI workloads
- [Azure OpenAI Embeddings Q&A](https://github.com/ruoccofabrizio/azure-open-ai-embeddings-qna) - OpenAI and Redis as a Q&A service on Azure.
- [ArXiv Paper Search](https://github.com/RedisVentures/redis-arXiv-search) - Semantic search over arXiv scholarly papers
- [Vector Search on Azure](https://learn.microsoft.com/azure/azure-cache-for-redis/cache-tutorial-vector-similarity) - Vector search on Azure using Azure Cache for Redis and Azure OpenAI


## More resources

For more information on how to use Redis as a vector database, check out the following resources:

- [RedisVL Documentation](https://redisvl.com) - Documentation for the Redis Vector Library Client
- [Redis Vector Similarity Docs](https://redis.io/docs/stack/search/reference/vectors/) - Redis official docs for Vector Search.
- [Redis-py Search Docs](https://redis.readthedocs.io/en/latest/redismodules.html#redisearch-commands) - Documentation for redis-py client library
- [Vector Similarity Search: From Basics to Production](https://mlops.community/vector-similarity-search-from-basics-to-production/) - Introductory blog post to VSS and Redis as a VectorDB.

## Setup

`Redis-py` is the officially supported client by Redis. Recently released is the `RedisVL` client which is purpose-built for the Vector Database use cases. Both can be installed with pip.


```python
%pip install -qU redis redisvl langchain-community
```

### Deploy Redis locally

To locally deploy Redis, run:

```console
docker run -d -p 6379:6379 -p 8001:8001 redis/redis-stack:latest
```
If things are running correctly you should see a nice Redis UI at `http://localhost:8001`. See the [Deployment options](#deployment-options) section above for other ways to deploy.

### Redis connection Url schemas

Valid Redis Url schemas are:
1. `redis://`  - Connection to Redis standalone, unencrypted
2. `rediss://` - Connection to Redis standalone, with TLS encryption
3. `redis+sentinel://`  - Connection to Redis server via Redis Sentinel, unencrypted
4. `rediss+sentinel://` - Connection to Redis server via Redis Sentinel, booth connections with TLS encryption

More information about additional connection parameters can be found in the [redis-py documentation](https://redis-py.readthedocs.io/en/stable/connections.html).


```python
# connection to redis standalone at localhost, db 0, no password
redis_url = "redis://localhost:6379"
# connection to host "redis" port 7379 with db 2 and password "secret" (old style authentication scheme without username / pre 6.x)
redis_url = "redis://:secret@redis:7379/2"
# connection to host redis on default port with user "joe", pass "secret" using redis version 6+ ACLs
redis_url = "redis://joe:secret@redis/0"

# connection to sentinel at localhost with default group mymaster and db 0, no password
redis_url = "redis+sentinel://localhost:26379"
# connection to sentinel at host redis with default port 26379 and user "joe" with password "secret" with default group mymaster and db 0
redis_url = "redis+sentinel://joe:secret@redis"
# connection to sentinel, no auth with sentinel monitoring group "zone-1" and database 2
redis_url = "redis+sentinel://redis:26379/zone-1/2"

# connection to redis standalone at localhost, db 0, no password but with TLS support
redis_url = "rediss://localhost:6379"
# connection to redis sentinel at localhost and default port, db 0, no password
# but with TLS support for booth Sentinel and Redis server
redis_url = "rediss+sentinel://localhost"
```

If you want to get best in-class automated tracing of your model calls you can also set your [LangSmith](https://docs.smith.langchain.com/) API key by uncommenting below:


```python
# os.environ["LANGSMITH_API_KEY"] = getpass.getpass("Enter your LangSmith API key: ")
# os.environ["LANGSMITH_TRACING"] = "true"
```

## Initialization

The Redis VectorStore instance can be initialized in a number of ways. There are multiple class methods that can be used to initialize a Redis VectorStore instance.

- ``Redis.__init__`` - Initialize directly
- ``Redis.from_documents`` - Initialize from a list of ``Langchain.docstore.Document`` objects
- ``Redis.from_texts`` - Initialize from a list of texts (optionally with metadata)
- ``Redis.from_texts_return_keys`` - Initialize from a list of texts (optionally with metadata) and return the keys
- ``Redis.from_existing_index`` - Initialize from an existing Redis index

Below we will use the ``Redis.__init__`` method. 

import EmbeddingTabs from "@theme/EmbeddingTabs";

<EmbeddingTabs/>


```python
<!--IMPORTS:[{"imported": "Redis", "source": "langchain_community.vectorstores.redis", "docs": "https://api.python.langchain.com/en/latest/vectorstores/langchain_community.vectorstores.redis.base.Redis.html", "title": "Redis"}]-->
from langchain_community.vectorstores.redis import Redis

vector_store = Redis(
    redis_url="redis://localhost:6379",
    embedding=embeddings,
    index_name="users",
)
```

## Manage vector store

Once you have created your vector store, we can interact with it by adding and deleting different items.

### Add items to vector store

We can add items to our vector store by using the `add_documents` function.


```python
<!--IMPORTS:[{"imported": "Document", "source": "langchain_core.documents", "docs": "https://api.python.langchain.com/en/latest/documents/langchain_core.documents.base.Document.html", "title": "Redis"}]-->
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
['doc:users:622f5f19-9b4b-4896-9a16-e1e95f19db4b',
 'doc:users:032b489f-d37e-4bf1-85ec-4c2275be48ef',
 'doc:users:5daf0855-b352-45bd-9d29-e21ff66e38c8',
 'doc:users:b9204897-190b-4dd9-af2b-081ed4e9cbb0',
 'doc:users:9395caff-1a6a-46c1-bc5c-7c5558eadf46',
 'doc:users:28243c3d-463d-4662-936e-003a2dc0dc30',
 'doc:users:1e1cdb91-c226-4836-b38e-ee4b61444913',
 'doc:users:4005bba2-2a08-4160-a16f-5cc3cf9d4aea',
 'doc:users:8c88440a-06d2-4a68-95f1-c58d0cf99d29',
 'doc:users:cc20438f-741a-40fd-bed8-4f1cee113680']
```


### Delete items from vector store


```python
vector_store.delete(ids=[uuids[-1]])
```



```output
True
```


### Inspecting the created Index

Once the ``Redis`` VectorStore object has been constructed, an index will have been created in Redis if it did not already exist. The index can be inspected with both the ``rvl``and the ``redis-cli`` command line tool. If you installed ``redisvl`` above, you can use the ``rvl`` command line tool to inspect the index.


```python
# assumes you're running Redis locally (use --host, --port, --password, --username, to change this)
!rvl index listall --port 6379
```
```output
[32m17:24:03[0m [34m[RedisVL][0m [1;30mINFO[0m   Indices:
[32m17:24:03[0m [34m[RedisVL][0m [1;30mINFO[0m   1. users
```
The ``Redis`` VectorStore implementation will attempt to generate index schema (fields for filtering) for any metadata passed through the ``from_texts``, ``from_texts_return_keys``, and ``from_documents`` methods. This way, whatever metadata is passed will be indexed into the Redis search index allowing
for filtering on those fields.

Below we show what fields were created from the metadata we defined above


```python
!rvl index info -i users --port 6379
```
```output


Index Information:
╭──────────────┬────────────────┬───────────────┬─────────────────┬────────────╮
│ Index Name   │ Storage Type   │ Prefixes      │ Index Options   │   Indexing │
├──────────────┼────────────────┼───────────────┼─────────────────┼────────────┤
│ users        │ HASH           │ ['doc:users'] │ []              │          0 │
╰──────────────┴────────────────┴───────────────┴─────────────────┴────────────╯
Index Fields:
╭────────────────┬────────────────┬────────┬────────────────┬────────────────┬────────────────┬────────────────┬────────────────┬────────────────┬─────────────────┬────────────────╮
│ Name           │ Attribute      │ Type   │ Field Option   │ Option Value   │ Field Option   │ Option Value   │ Field Option   │   Option Value │ Field Option    │ Option Value   │
├────────────────┼────────────────┼────────┼────────────────┼────────────────┼────────────────┼────────────────┼────────────────┼────────────────┼─────────────────┼────────────────┤
│ content        │ content        │ TEXT   │ WEIGHT         │ 1              │                │                │                │                │                 │                │
│ content_vector │ content_vector │ VECTOR │ algorithm      │ FLAT           │ data_type      │ FLOAT32        │ dim            │           3072 │ distance_metric │ COSINE         │
╰────────────────┴────────────────┴────────┴────────────────┴────────────────┴────────────────┴────────────────┴────────────────┴────────────────┴─────────────────┴────────────────╯
```

```python
!rvl stats -i users --port 6379
```
```output

Statistics:
╭─────────────────────────────┬─────────────╮
│ Stat Key                    │ Value       │
├─────────────────────────────┼─────────────┤
│ num_docs                    │ 10          │
│ num_terms                   │ 100         │
│ max_doc_id                  │ 10          │
│ num_records                 │ 116         │
│ percent_indexed             │ 1           │
│ hash_indexing_failures      │ 0           │
│ number_of_uses              │ 1           │
│ bytes_per_record_avg        │ 88.2931     │
│ doc_table_size_mb           │ 0.00108719  │
│ inverted_sz_mb              │ 0.00976753  │
│ key_table_size_mb           │ 0.000304222 │
│ offset_bits_per_record_avg  │ 8           │
│ offset_vectors_sz_mb        │ 0.000102043 │
│ offsets_per_term_avg        │ 0.922414    │
│ records_per_doc_avg         │ 11.6        │
│ sortable_values_size_mb     │ 0           │
│ total_indexing_time         │ 1.373       │
│ total_inverted_index_blocks │ 100         │
│ vector_index_sz_mb          │ 12.0086     │
╰─────────────────────────────┴─────────────╯
```
It's important to note that we have not specified that the ``user``, ``job``, ``credit_score`` and ``age`` in the metadata should be fields within the index, this is because the ``Redis`` VectorStore object automatically generate the index schema from the passed metadata. For more information on the generation of index fields, see the API documentation.

## Query vector store

Once your vector store has been created and the relevant documents have been added you will most likely wish to query it during the running of your chain or agent. 

### Query directly

#### Similarity search

Performing a simple similarity search can be done as follows:


```python
results = vector_store.similarity_search(
    "LangChain provides abstractions to make working with LLMs easy", k=2
)
for res in results:
    print(f"* {res.page_content} [{res.metadata}]")
```
```output
* Building an exciting new project with LangChain - come check it out! [{'id': 'doc:users:5daf0855-b352-45bd-9d29-e21ff66e38c8'}]
* LangGraph is the best framework for building stateful, agentic applications! [{'id': 'doc:users:4005bba2-2a08-4160-a16f-5cc3cf9d4aea'}]
```
#### Similarity search with score

You can also search with score:


```python
results = vector_store.similarity_search_with_score("Will it be hot tomorrow?", k=1)
for res, score in results:
    print(f"* [SIM={score:3f}] {res.page_content} [{res.metadata}]")
```
```output
* [SIM=0.446900] The weather forecast for tomorrow is cloudy and overcast, with a high of 62 degrees. [{'id': 'doc:users:032b489f-d37e-4bf1-85ec-4c2275be48ef'}]
```
#### Other search methods

For a list of all the search functions available to the `Redis` vector store, please refer to the [API reference](https://api.python.langchain.com/en/latest/vectorstores/langchain_community.vectorstores.redis.base.Redis.html)

## Connect to an existing Index

In order to have the same metadata indexed when using the ``Redis`` VectorStore. You will need to have the same ``index_schema`` passed in either as a path to a yaml file or as a dictionary. The following shows how to obtain the schema from an index and connect to an existing index.


```python
# write the schema to a yaml file
vector_store.write_schema("redis_schema.yaml")
```


```python
# now we can connect to our existing index as follows

new_rds = Redis.from_existing_index(
    embeddings,
    index_name="users",
    redis_url="redis://localhost:6379",
    schema="redis_schema.yaml",
)
results = new_rds.similarity_search("foo", k=3)
print(results[0].metadata)
```
```output
{'id': 'doc:users:8484c48a032d4c4cbe3cc2ed6845fabb', 'user': 'john', 'job': 'engineer', 'credit_score': 'high', 'age': '18'}
```

```python
# see the schemas are the same
new_rds.schema == vector_store.schema
```



```output
True
```


## Custom metadata indexing

In some cases, you may want to control what fields the metadata maps to. For example, you may want the ``credit_score`` field to be a categorical field instead of a text field (which is the default behavior for all string fields). In this case, you can use the ``index_schema`` parameter in each of the initialization methods above to specify the schema for the index. Custom index schema can either be passed as a dictionary or as a path to a YAML file.

All arguments in the schema have defaults besides the name, so you can specify only the fields you want to change. All the names correspond to the snake/lowercase versions of the arguments you would use on the command line with ``redis-cli`` or in ``redis-py``. For more on the arguments for each field, see the [documentation](https://redis.io/docs/interact/search-and-query/basic-constructs/field-and-type-options/)

The below example shows how to specify the schema for the ``credit_score`` field as a Tag (categorical) field instead of a text field. 

```yaml
# index_schema.yml
tag:
    - name: credit_score
text:
    - name: user
    - name: job
numeric:
    - name: age
```

In Python, this would look like:

```python

index_schema = {
    "tag": [{"name": "credit_score"}],
    "text": [{"name": "user"}, {"name": "job"}],
    "numeric": [{"name": "age"}],
}

```

Notice that only the ``name`` field needs to be specified. All other fields have defaults.


```python
# create a new index with the new schema defined above
index_schema = {
    "tag": [{"name": "credit_score"}],
    "text": [{"name": "user"}, {"name": "job"}],
    "numeric": [{"name": "age"}],
}
texts = []  # list of texts
metadata = {}  # dictionary of metadata

rds, keys = Redis.from_texts_return_keys(
    texts,
    embeddings,
    metadatas=metadata,
    redis_url="redis://localhost:6379",
    index_name="users_modified",
    index_schema=index_schema,  # pass in the new index schema
)
```
```output
`index_schema` does not match generated metadata schema.
If you meant to manually override the schema, please ignore this message.
index_schema: {'tag': [{'name': 'credit_score'}], 'text': [{'name': 'user'}, {'name': 'job'}], 'numeric': [{'name': 'age'}]}
generated_schema: {'text': [{'name': 'user'}, {'name': 'job'}, {'name': 'credit_score'}], 'numeric': [{'name': 'age'}], 'tag': []}
```
The above warning is meant to notify users when they are overriding the default behavior. Ignore it if you are intentionally overriding the behavior.

## Hybrid filtering

With the Redis Filter Expression language built into LangChain, you can create arbitrarily long chains of hybrid filters
that can be used to filter your search results. The expression language is derived from the [RedisVL Expression Syntax](https://redisvl.com)
and is designed to be easy to use and understand.

The following are the available filter types:
- ``RedisText``: Filter by full-text search against metadata fields. Supports exact, fuzzy, and wildcard matching.
- ``RedisNum``: Filter by numeric range against metadata fields.
- ``RedisTag``: Filter by the exact match against string-based categorical metadata fields. Multiple tags can be specified like "tag1,tag2,tag3".

The following are examples of utilizing these filters.

```python
<!--IMPORTS:[{"imported": "RedisText", "source": "langchain_community.vectorstores.redis", "docs": "https://api.python.langchain.com/en/latest/vectorstores/langchain_community.vectorstores.redis.filters.RedisText.html", "title": "Redis"}, {"imported": "RedisNum", "source": "langchain_community.vectorstores.redis", "docs": "https://api.python.langchain.com/en/latest/vectorstores/langchain_community.vectorstores.redis.filters.RedisNum.html", "title": "Redis"}, {"imported": "RedisTag", "source": "langchain_community.vectorstores.redis", "docs": "https://api.python.langchain.com/en/latest/vectorstores/langchain_community.vectorstores.redis.filters.RedisTag.html", "title": "Redis"}]-->

from langchain_community.vectorstores.redis import RedisText, RedisNum, RedisTag

# exact matching
has_high_credit = RedisTag("credit_score") == "high"
does_not_have_high_credit = RedisTag("credit_score") != "low"

# fuzzy matching
job_starts_with_eng = RedisText("job") % "eng*"
job_is_engineer = RedisText("job") == "engineer"
job_is_not_engineer = RedisText("job") != "engineer"

# numeric filtering
age_is_18 = RedisNum("age") == 18
age_is_not_18 = RedisNum("age") != 18
age_is_greater_than_18 = RedisNum("age") > 18
age_is_less_than_18 = RedisNum("age") < 18
age_is_greater_than_or_equal_to_18 = RedisNum("age") >= 18
age_is_less_than_or_equal_to_18 = RedisNum("age") <= 18

```

The ``RedisFilter`` class can be used to simplify the import of these filters as follows

```python
<!--IMPORTS:[{"imported": "RedisFilter", "source": "langchain_community.vectorstores.redis", "docs": "https://api.python.langchain.com/en/latest/vectorstores/langchain_community.vectorstores.redis.filters.RedisFilter.html", "title": "Redis"}]-->

from langchain_community.vectorstores.redis import RedisFilter

# same examples as above
has_high_credit = RedisFilter.tag("credit_score") == "high"
does_not_have_high_credit = RedisFilter.num("age") > 8
job_starts_with_eng = RedisFilter.text("job") % "eng*"
```

The following are examples of using a hybrid filter for search


```python
<!--IMPORTS:[{"imported": "RedisText", "source": "langchain_community.vectorstores.redis", "docs": "https://api.python.langchain.com/en/latest/vectorstores/langchain_community.vectorstores.redis.filters.RedisText.html", "title": "Redis"}]-->
from langchain_community.vectorstores.redis import RedisText

is_engineer = RedisText("job") == "engineer"
results = rds.similarity_search("foo", k=3, filter=is_engineer)

print("Job:", results[0].metadata["job"])
print("Engineers in the dataset:", len(results))
```
```output
Job: engineer
Engineers in the dataset: 2
```

```python
# fuzzy match
starts_with_doc = RedisText("job") % "doc*"
results = rds.similarity_search("foo", k=3, filter=starts_with_doc)

for result in results:
    print("Job:", result.metadata["job"])
print("Jobs in dataset that start with 'doc':", len(results))
```
```output
Job: doctor
Job: doctor
Jobs in dataset that start with 'doc': 2
```

```python
<!--IMPORTS:[{"imported": "RedisNum", "source": "langchain_community.vectorstores.redis", "docs": "https://api.python.langchain.com/en/latest/vectorstores/langchain_community.vectorstores.redis.filters.RedisNum.html", "title": "Redis"}]-->
from langchain_community.vectorstores.redis import RedisNum

is_over_18 = RedisNum("age") > 18
is_under_99 = RedisNum("age") < 99
age_range = is_over_18 & is_under_99
results = rds.similarity_search("foo", filter=age_range)

for result in results:
    print("User:", result.metadata["user"], "is", result.metadata["age"])
```
```output
User: derrick is 45
User: nancy is 94
User: joe is 35
```

```python
# make sure to use parenthesis around FilterExpressions
# if initializing them while constructing them
age_range = (RedisNum("age") > 18) & (RedisNum("age") < 99)
results = rds.similarity_search("foo", filter=age_range)

for result in results:
    print("User:", result.metadata["user"], "is", result.metadata["age"])
```
```output
User: derrick is 45
User: nancy is 94
User: joe is 35
```
### Query by turning into retriever

You can also transform the vector store into a retriever for easier usage in your chains. Here we go over different options for using the vector store as a retriever.

There are three different search methods we can use to do retrieval. By default, it will use semantic similarity. To see all the options, please refer to the [API reference](https://api.python.langchain.com/en/latest/vectorstores/langchain_community.vectorstores.redis.base.Redis.html#langchain_community.vectorstores.redis.base.Redis.as_retriever)


```python
retriever = vector_store.as_retriever(
    search_type="similarity_score_threshold",
    search_kwargs={"k": 1, "score_threshold": 0.2},
)
retriever.invoke("Stealing from the bank is a crime")
```



```output
[Document(metadata={'id': 'doc:users:b9204897-190b-4dd9-af2b-081ed4e9cbb0'}, page_content='Robbers broke into the city bank and stole $1 million in cash.')]
```


## Usage for retrieval-augmented generation

For guides on how to use this vector store for retrieval-augmented generation (RAG), see the following sections:

- [Tutorials: working with external knowledge](https://python.langchain.com/v0.2/docs/tutorials/#working-with-external-knowledge)
- [How-to: Question and answer with RAG](https://python.langchain.com/v0.2/docs/how_to/#qa-with-rag)
- [Retrieval conceptual docs](https://python.langchain.com/v0.2/docs/concepts/#retrieval)

## API reference

For detailed documentation of all `Redis` vector store features and configurations head to the API reference: https://api.python.langchain.com/en/latest/vectorstores/langchain_community.vectorstores.redis.base.Redis.html


## Related

- Vector store [conceptual guide](/docs/concepts/#vector-stores)
- Vector store [how-to guides](/docs/how_to/#vector-stores)