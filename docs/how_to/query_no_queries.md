---
canonical: https://python.langchain.com/v0.2/docs/how_to/query_no_queries/
custom_edit_url: https://github.com/langchain-ai/langchain/edit/master/docs/docs/how_to/query_no_queries.ipynb
sidebar_position: 3
---

# How to handle cases where no queries are generated

Sometimes, a query analysis technique may allow for any number of queries to be generated - including no queries! In this case, our overall chain will need to inspect the result of the query analysis before deciding whether to call the retriever or not.

We will use mock data for this example.

## Setup
#### Install dependencies


```python
# %pip install -qU langchain langchain-community langchain-openai langchain-chroma
```

#### Set environment variables

We'll use OpenAI in this example:


```python
import getpass
import os

os.environ["OPENAI_API_KEY"] = getpass.getpass()

# Optional, uncomment to trace runs with LangSmith. Sign up here: https://smith.langchain.com.
# os.environ["LANGCHAIN_TRACING_V2"] = "true"
# os.environ["LANGCHAIN_API_KEY"] = getpass.getpass()
```

### Create Index

We will create a vectorstore over fake information.


```python
<!--IMPORTS:[{"imported": "Chroma", "source": "langchain_chroma", "docs": "https://api.python.langchain.com/en/latest/vectorstores/langchain_chroma.vectorstores.Chroma.html", "title": "How to handle cases where no queries are generated"}, {"imported": "OpenAIEmbeddings", "source": "langchain_openai", "docs": "https://api.python.langchain.com/en/latest/embeddings/langchain_openai.embeddings.base.OpenAIEmbeddings.html", "title": "How to handle cases where no queries are generated"}, {"imported": "RecursiveCharacterTextSplitter", "source": "langchain_text_splitters", "docs": "https://api.python.langchain.com/en/latest/character/langchain_text_splitters.character.RecursiveCharacterTextSplitter.html", "title": "How to handle cases where no queries are generated"}]-->
from langchain_chroma import Chroma
from langchain_openai import OpenAIEmbeddings
from langchain_text_splitters import RecursiveCharacterTextSplitter

texts = ["Harrison worked at Kensho"]
embeddings = OpenAIEmbeddings(model="text-embedding-3-small")
vectorstore = Chroma.from_texts(
    texts,
    embeddings,
)
retriever = vectorstore.as_retriever()
```

## Query analysis

We will use function calling to structure the output. However, we will configure the LLM such that is doesn't NEED to call the function representing a search query (should it decide not to). We will also then use a prompt to do query analysis that explicitly lays when it should and shouldn't make a search.


```python
from typing import Optional

from langchain_core.pydantic_v1 import BaseModel, Field


class Search(BaseModel):
    """Search over a database of job records."""

    query: str = Field(
        ...,
        description="Similarity search query applied to job record.",
    )
```


```python
<!--IMPORTS:[{"imported": "ChatPromptTemplate", "source": "langchain_core.prompts", "docs": "https://api.python.langchain.com/en/latest/prompts/langchain_core.prompts.chat.ChatPromptTemplate.html", "title": "How to handle cases where no queries are generated"}, {"imported": "RunnablePassthrough", "source": "langchain_core.runnables", "docs": "https://api.python.langchain.com/en/latest/runnables/langchain_core.runnables.passthrough.RunnablePassthrough.html", "title": "How to handle cases where no queries are generated"}, {"imported": "ChatOpenAI", "source": "langchain_openai", "docs": "https://api.python.langchain.com/en/latest/chat_models/langchain_openai.chat_models.base.ChatOpenAI.html", "title": "How to handle cases where no queries are generated"}]-->
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.runnables import RunnablePassthrough
from langchain_openai import ChatOpenAI

system = """You have the ability to issue search queries to get information to help answer user information.

You do not NEED to look things up. If you don't need to, then just respond normally."""
prompt = ChatPromptTemplate.from_messages(
    [
        ("system", system),
        ("human", "{question}"),
    ]
)
llm = ChatOpenAI(model="gpt-3.5-turbo-0125", temperature=0)
structured_llm = llm.bind_tools([Search])
query_analyzer = {"question": RunnablePassthrough()} | prompt | structured_llm
```

We can see that by invoking this we get an message that sometimes - but not always - returns a tool call.


```python
query_analyzer.invoke("where did Harrison Work")
```



```output
AIMessage(content='', additional_kwargs={'tool_calls': [{'id': 'call_ZnoVX4j9Mn8wgChaORyd1cvq', 'function': {'arguments': '{"query":"Harrison"}', 'name': 'Search'}, 'type': 'function'}]})
```



```python
query_analyzer.invoke("hi!")
```



```output
AIMessage(content='Hello! How can I assist you today?')
```


## Retrieval with query analysis

So how would we include this in a chain? Let's look at an example below.


```python
<!--IMPORTS:[{"imported": "PydanticToolsParser", "source": "langchain_core.output_parsers.openai_tools", "docs": "https://api.python.langchain.com/en/latest/output_parsers/langchain_core.output_parsers.openai_tools.PydanticToolsParser.html", "title": "How to handle cases where no queries are generated"}, {"imported": "chain", "source": "langchain_core.runnables", "docs": "https://api.python.langchain.com/en/latest/runnables/langchain_core.runnables.base.chain.html", "title": "How to handle cases where no queries are generated"}]-->
from langchain_core.output_parsers.openai_tools import PydanticToolsParser
from langchain_core.runnables import chain

output_parser = PydanticToolsParser(tools=[Search])
```


```python
@chain
def custom_chain(question):
    response = query_analyzer.invoke(question)
    if "tool_calls" in response.additional_kwargs:
        query = output_parser.invoke(response)
        docs = retriever.invoke(query[0].query)
        # Could add more logic - like another LLM call - here
        return docs
    else:
        return response
```


```python
custom_chain.invoke("where did Harrison Work")
```
```output
Number of requested results 4 is greater than number of elements in index 1, updating n_results = 1
```


```output
[Document(page_content='Harrison worked at Kensho')]
```



```python
custom_chain.invoke("hi!")
```



```output
AIMessage(content='Hello! How can I assist you today?')
```