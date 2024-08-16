---
canonical: https://python.langchain.com/v0.2/docs/integrations/llms/cohere/
custom_edit_url: https://github.com/langchain-ai/langchain/edit/master/docs/docs/integrations/llms/cohere.ipynb
---

# Cohere

:::caution
You are currently on a page documenting the use of Cohere models as [text completion models](/docs/concepts/#llms). Many popular Cohere models are [chat completion models](/docs/concepts/#chat-models).

You may be looking for [this page instead](/docs/integrations/chat/cohere/).
:::

>[Cohere](https://cohere.ai/about) is a Canadian startup that provides natural language processing models that help companies improve human-machine interactions.

Head to the [API reference](https://api.python.langchain.com/en/latest/llms/langchain_community.llms.cohere.Cohere.html) for detailed documentation of all attributes and methods.

## Overview
### Integration details

| Class | Package | Local | Serializable | [JS support](https://js.langchain.com/v0.2/docs/integrations/llms/cohere/) | Package downloads | Package latest |
| :--- | :--- | :---: | :---: |  :---: | :---: | :---: |
| [Cohere](https://api.python.langchain.com/en/latest/llms/langchain_community.llms.cohere.Cohere.html) | [langchain_community](https://api.python.langchain.com/en/latest/community_api_reference.html) | ❌ | beta | ✅ | ![PyPI - Downloads](https://img.shields.io/pypi/dm/langchain_community?style=flat-square&label=%20) | ![PyPI - Version](https://img.shields.io/pypi/v/langchain_community?style=flat-square&label=%20) |


## Setup

The integration lives in the `langchain-community` package. We also need to install the `cohere` package itself. We can install these with:

### Credentials

We'll need to get a [Cohere API key](https://cohere.com/) and set the `COHERE_API_KEY` environment variable:


```python
import getpass
import os

if "COHERE_API_KEY" not in os.environ:
    os.environ["COHERE_API_KEY"] = getpass.getpass()
```

### Installation


```python
pip install -U langchain-community langchain-cohere
```

It's also helpful (but not needed) to set up [LangSmith](https://smith.langchain.com/) for best-in-class observability


```python
# os.environ["LANGCHAIN_TRACING_V2"] = "true"
# os.environ["LANGCHAIN_API_KEY"] = getpass.getpass()
```

## Invocation

Cohere supports all [LLM](/docs/how_to#llms) functionality:


```python
<!--IMPORTS:[{"imported": "HumanMessage", "source": "langchain_core.messages", "docs": "https://api.python.langchain.com/en/latest/messages/langchain_core.messages.human.HumanMessage.html", "title": "Cohere"}]-->
from langchain_cohere import Cohere
from langchain_core.messages import HumanMessage
```


```python
model = Cohere(max_tokens=256, temperature=0.75)
```


```python
message = "Knock knock"
model.invoke(message)
```



```output
" Who's there?"
```



```python
await model.ainvoke(message)
```



```output
" Who's there?"
```



```python
for chunk in model.stream(message):
    print(chunk, end="", flush=True)
```
```output
 Who's there?
```

```python
model.batch([message])
```



```output
[" Who's there?"]
```


## Chaining

You can also easily combine with a prompt template for easy structuring of user input. We can do this using [LCEL](/docs/concepts#langchain-expression-language-lcel)


```python
<!--IMPORTS:[{"imported": "PromptTemplate", "source": "langchain_core.prompts", "docs": "https://api.python.langchain.com/en/latest/prompts/langchain_core.prompts.prompt.PromptTemplate.html", "title": "Cohere"}]-->
from langchain_core.prompts import PromptTemplate

prompt = PromptTemplate.from_template("Tell me a joke about {topic}")
chain = prompt | model
```


```python
chain.invoke({"topic": "bears"})
```



```output
' Why did the teddy bear cross the road?\nBecause he had bear crossings.\n\nWould you like to hear another joke? '
```


## API reference

For detailed documentation of all `Cohere` llm features and configurations head to the API reference: https://api.python.langchain.com/en/latest/llms/langchain_community.llms.cohere.Cohere.html


## Related

- LLM [conceptual guide](/docs/concepts/#llms)
- LLM [how-to guides](/docs/how_to/#llms)