---
canonical: https://python.langchain.com/v0.2/docs/versions/migrating_chains/llm_router_chain/
custom_edit_url: https://github.com/langchain-ai/langchain/edit/master/docs/docs/versions/migrating_chains/llm_router_chain.ipynb
title: Migrating from LLMRouterChain
---

The [`LLMRouterChain`](https://api.python.langchain.com/en/latest/chains/langchain.chains.router.llm_router.LLMRouterChain.html) routed an input query to one of multiple destinations-- that is, given an input query, it used a LLM to select from a list of destination chains, and passed its inputs to the selected chain.

`LLMRouterChain` does not support common [chat model](/docs/concepts/#chat-models) features, such as message roles and [tool calling](/docs/concepts/#functiontool-calling). Under the hood, `LLMRouterChain` routes a query by instructing the LLM to generate JSON-formatted text, and parsing out the intended destination.

Consider an example from a [MultiPromptChain](/docs/versions/migrating_chains/multi_prompt_chain), which uses `LLMRouterChain`. Below is an (example) default prompt:


```python
from langchain.chains.router.multi_prompt import MULTI_PROMPT_ROUTER_TEMPLATE

destinations = """
animals: prompt for animal expert
vegetables: prompt for a vegetable expert
"""

router_template = MULTI_PROMPT_ROUTER_TEMPLATE.format(destinations=destinations)

print(router_template.replace("`", "'"))  # for rendering purposes
```
```output
Given a raw text input to a language model select the model prompt best suited for the input. You will be given the names of the available prompts and a description of what the prompt is best suited for. You may also revise the original input if you think that revising it will ultimately lead to a better response from the language model.

<< FORMATTING >>
Return a markdown code snippet with a JSON object formatted to look like:
'''json
{{
    "destination": string \ name of the prompt to use or "DEFAULT"
    "next_inputs": string \ a potentially modified version of the original input
}}
'''

REMEMBER: "destination" MUST be one of the candidate prompt names specified below OR it can be "DEFAULT" if the input is not well suited for any of the candidate prompts.
REMEMBER: "next_inputs" can just be the original input if you don't think any modifications are needed.

<< CANDIDATE PROMPTS >>

animals: prompt for animal expert
vegetables: prompt for a vegetable expert


<< INPUT >>
{input}

<< OUTPUT (must include '''json at the start of the response) >>
<< OUTPUT (must end with ''') >>
```
Most of the behavior is determined via a single natural language prompt. Chat models that support [tool calling](/docs/how_to/tool_calling/) features confer a number of advantages for this task:

- Supports chat prompt templates, including messages with `system` and other roles;
- Tool-calling models are fine-tuned to generate structured output;
- Support for runnable methods like streaming and async operations.

Now let's look at `LLMRouterChain` side-by-side with an LCEL implementation that uses tool-calling. Note that for this guide we will `langchain-openai >= 0.1.20`:


```python
%pip install -qU langchain-core langchain-openai
```


```python
import os
from getpass import getpass

os.environ["OPENAI_API_KEY"] = getpass()
```

## Legacy

<details open>


```python
<!--IMPORTS:[{"imported": "LLMRouterChain", "source": "langchain.chains.router.llm_router", "docs": "https://api.python.langchain.com/en/latest/chains/langchain.chains.router.llm_router.LLMRouterChain.html", "title": "# Legacy"}, {"imported": "RouterOutputParser", "source": "langchain.chains.router.llm_router", "docs": "https://api.python.langchain.com/en/latest/chains/langchain.chains.router.llm_router.RouterOutputParser.html", "title": "# Legacy"}, {"imported": "PromptTemplate", "source": "langchain_core.prompts", "docs": "https://api.python.langchain.com/en/latest/prompts/langchain_core.prompts.prompt.PromptTemplate.html", "title": "# Legacy"}, {"imported": "ChatOpenAI", "source": "langchain_openai", "docs": "https://api.python.langchain.com/en/latest/chat_models/langchain_openai.chat_models.base.ChatOpenAI.html", "title": "# Legacy"}]-->
from langchain.chains.router.llm_router import LLMRouterChain, RouterOutputParser
from langchain_core.prompts import PromptTemplate
from langchain_openai import ChatOpenAI

llm = ChatOpenAI(model="gpt-4o-mini")

router_prompt = PromptTemplate(
    # Note: here we use the prompt template from above. Generally this would need
    # to be customized.
    template=router_template,
    input_variables=["input"],
    output_parser=RouterOutputParser(),
)

chain = LLMRouterChain.from_llm(llm, router_prompt)
```


```python
result = chain.invoke({"input": "What color are carrots?"})

print(result["destination"])
```
```output
vegetables
```
</details>

## LCEL

<details open>


```python
<!--IMPORTS:[{"imported": "ChatPromptTemplate", "source": "langchain_core.prompts", "docs": "https://api.python.langchain.com/en/latest/prompts/langchain_core.prompts.chat.ChatPromptTemplate.html", "title": "# Legacy"}, {"imported": "RunnablePassthrough", "source": "langchain_core.runnables", "docs": "https://api.python.langchain.com/en/latest/runnables/langchain_core.runnables.passthrough.RunnablePassthrough.html", "title": "# Legacy"}, {"imported": "ChatOpenAI", "source": "langchain_openai", "docs": "https://api.python.langchain.com/en/latest/chat_models/langchain_openai.chat_models.base.ChatOpenAI.html", "title": "# Legacy"}]-->
from operator import itemgetter
from typing import Literal

from langchain_core.prompts import ChatPromptTemplate
from langchain_core.runnables import RunnablePassthrough
from langchain_openai import ChatOpenAI
from typing_extensions import TypedDict

llm = ChatOpenAI(model="gpt-4o-mini")

route_system = "Route the user's query to either the animal or vegetable expert."
route_prompt = ChatPromptTemplate.from_messages(
    [
        ("system", route_system),
        ("human", "{input}"),
    ]
)


# Define schema for output:
class RouteQuery(TypedDict):
    """Route query to destination expert."""

    destination: Literal["animal", "vegetable"]


# Instead of writing formatting instructions into the prompt, we
# leverage .with_structured_output to coerce the output into a simple
# schema.
chain = route_prompt | llm.with_structured_output(RouteQuery)
```


```python
result = chain.invoke({"input": "What color are carrots?"})

print(result["destination"])
```
```output
vegetable
```
</details>

## Next steps

See [this tutorial](/docs/tutorials/llm_chain) for more detail on building with prompt templates, LLMs, and output parsers.

Check out the [LCEL conceptual docs](/docs/concepts/#langchain-expression-language-lcel) for more background information.