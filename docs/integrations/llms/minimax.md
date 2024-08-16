---
canonical: https://python.langchain.com/v0.2/docs/integrations/llms/minimax/
custom_edit_url: https://github.com/langchain-ai/langchain/edit/master/docs/docs/integrations/llms/minimax.ipynb
---

# Minimax

[Minimax](https://api.minimax.chat) is a Chinese startup that provides natural language processing models for companies and individuals.

This example demonstrates using Langchain to interact with Minimax.

# Setup

To run this notebook, you'll need a [Minimax account](https://api.minimax.chat), an [API key](https://api.minimax.chat/user-center/basic-information/interface-key), and a [Group ID](https://api.minimax.chat/user-center/basic-information)

# Single model call


```python
<!--IMPORTS:[{"imported": "Minimax", "source": "langchain_community.llms", "docs": "https://api.python.langchain.com/en/latest/llms/langchain_community.llms.minimax.Minimax.html", "title": "Minimax"}]-->
from langchain_community.llms import Minimax
```


```python
# Load the model
minimax = Minimax(minimax_api_key="YOUR_API_KEY", minimax_group_id="YOUR_GROUP_ID")
```


```python
# Prompt the model
minimax("What is the difference between panda and bear?")
```

# Chained model calls


```python
# get api_key and group_id: https://api.minimax.chat/user-center/basic-information
# We need `MINIMAX_API_KEY` and `MINIMAX_GROUP_ID`

import os

os.environ["MINIMAX_API_KEY"] = "YOUR_API_KEY"
os.environ["MINIMAX_GROUP_ID"] = "YOUR_GROUP_ID"
```


```python
<!--IMPORTS:[{"imported": "LLMChain", "source": "langchain.chains", "docs": "https://api.python.langchain.com/en/latest/chains/langchain.chains.llm.LLMChain.html", "title": "Minimax"}, {"imported": "Minimax", "source": "langchain_community.llms", "docs": "https://api.python.langchain.com/en/latest/llms/langchain_community.llms.minimax.Minimax.html", "title": "Minimax"}, {"imported": "PromptTemplate", "source": "langchain_core.prompts", "docs": "https://api.python.langchain.com/en/latest/prompts/langchain_core.prompts.prompt.PromptTemplate.html", "title": "Minimax"}]-->
from langchain.chains import LLMChain
from langchain_community.llms import Minimax
from langchain_core.prompts import PromptTemplate
```


```python
template = """Question: {question}

Answer: Let's think step by step."""

prompt = PromptTemplate.from_template(template)
```


```python
llm = Minimax()
```


```python
llm_chain = LLMChain(prompt=prompt, llm=llm)
```


```python
question = "What NBA team won the Championship in the year Jay Zhou was born?"

llm_chain.run(question)
```


## Related

- LLM [conceptual guide](/docs/concepts/#llms)
- LLM [how-to guides](/docs/how_to/#llms)