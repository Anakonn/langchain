---
canonical: https://python.langchain.com/v0.2/docs/versions/migrating_chains/llm_chain/
custom_edit_url: https://github.com/langchain-ai/langchain/edit/master/docs/docs/versions/migrating_chains/llm_chain.ipynb
title: Migrating from LLMChain
---

[`LLMChain`](https://api.python.langchain.com/en/latest/chains/langchain.chains.llm.LLMChain.html) combined a prompt template, LLM, and output parser into a class.

Some advantages of switching to the LCEL implementation are:

- Clarity around contents and parameters. The legacy `LLMChain` contains a default output parser and other options.
- Easier streaming. `LLMChain` only supports streaming via callbacks.
- Easier access to raw message outputs if desired. `LLMChain` only exposes these via a parameter or via callback.


```python
%pip install --upgrade --quiet langchain-openai
```


```python
import os
from getpass import getpass

os.environ["OPENAI_API_KEY"] = getpass()
```

## Legacy

<details open>


```python
<!--IMPORTS:[{"imported": "LLMChain", "source": "langchain.chains", "docs": "https://api.python.langchain.com/en/latest/chains/langchain.chains.llm.LLMChain.html", "title": "# Legacy"}, {"imported": "ChatPromptTemplate", "source": "langchain_core.prompts", "docs": "https://api.python.langchain.com/en/latest/prompts/langchain_core.prompts.chat.ChatPromptTemplate.html", "title": "# Legacy"}, {"imported": "ChatOpenAI", "source": "langchain_openai", "docs": "https://api.python.langchain.com/en/latest/chat_models/langchain_openai.chat_models.base.ChatOpenAI.html", "title": "# Legacy"}]-->
from langchain.chains import LLMChain
from langchain_core.prompts import ChatPromptTemplate
from langchain_openai import ChatOpenAI

prompt = ChatPromptTemplate.from_messages(
    [("user", "Tell me a {adjective} joke")],
)

chain = LLMChain(llm=ChatOpenAI(), prompt=prompt)

chain({"adjective": "funny"})
```



```output
{'adjective': 'funny',
 'text': "Why couldn't the bicycle stand up by itself?\n\nBecause it was two tired!"}
```


</details>

## LCEL

<details open>


```python
<!--IMPORTS:[{"imported": "StrOutputParser", "source": "langchain_core.output_parsers", "docs": "https://api.python.langchain.com/en/latest/output_parsers/langchain_core.output_parsers.string.StrOutputParser.html", "title": "# Legacy"}, {"imported": "ChatPromptTemplate", "source": "langchain_core.prompts", "docs": "https://api.python.langchain.com/en/latest/prompts/langchain_core.prompts.chat.ChatPromptTemplate.html", "title": "# Legacy"}, {"imported": "ChatOpenAI", "source": "langchain_openai", "docs": "https://api.python.langchain.com/en/latest/chat_models/langchain_openai.chat_models.base.ChatOpenAI.html", "title": "# Legacy"}]-->
from langchain_core.output_parsers import StrOutputParser
from langchain_core.prompts import ChatPromptTemplate
from langchain_openai import ChatOpenAI

prompt = ChatPromptTemplate.from_messages(
    [("user", "Tell me a {adjective} joke")],
)

chain = prompt | ChatOpenAI() | StrOutputParser()

chain.invoke({"adjective": "funny"})
```



```output
'Why was the math book sad?\n\nBecause it had too many problems.'
```


Note that `LLMChain` by default returns a `dict` containing both the input and the output. If this behavior is desired, we can replicate it using another LCEL primitive, [`RunnablePassthrough`](https://api.python.langchain.com/en/latest/runnables/langchain_core.runnables.passthrough.RunnablePassthrough.html):


```python
<!--IMPORTS:[{"imported": "RunnablePassthrough", "source": "langchain_core.runnables", "docs": "https://api.python.langchain.com/en/latest/runnables/langchain_core.runnables.passthrough.RunnablePassthrough.html", "title": "# Legacy"}]-->
from langchain_core.runnables import RunnablePassthrough

outer_chain = RunnablePassthrough().assign(text=chain)

outer_chain.invoke({"adjective": "funny"})
```



```output
{'adjective': 'funny',
 'text': 'Why did the scarecrow win an award? Because he was outstanding in his field!'}
```


</details>

## Next steps

See [this tutorial](/docs/tutorials/llm_chain) for more detail on building with prompt templates, LLMs, and output parsers.

Check out the [LCEL conceptual docs](/docs/concepts/#langchain-expression-language-lcel) for more background information.