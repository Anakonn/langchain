---
canonical: https://python.langchain.com/v0.2/docs/integrations/llms/stochasticai/
custom_edit_url: https://github.com/langchain-ai/langchain/edit/master/docs/docs/integrations/llms/stochasticai.ipynb
---

# StochasticAI

>[Stochastic Acceleration Platform](https://docs.stochastic.ai/docs/introduction/) aims to simplify the life cycle of a Deep Learning model. From uploading and versioning the model, through training, compression and acceleration to putting it into production.

This example goes over how to use LangChain to interact with `StochasticAI` models.

You have to get the API_KEY and the API_URL [here](https://app.stochastic.ai/workspace/profile/settings?tab=profile).


```python
from getpass import getpass

STOCHASTICAI_API_KEY = getpass()
```
```output
 ········
```

```python
import os

os.environ["STOCHASTICAI_API_KEY"] = STOCHASTICAI_API_KEY
```


```python
YOUR_API_URL = getpass()
```
```output
 ········
```

```python
<!--IMPORTS:[{"imported": "LLMChain", "source": "langchain.chains", "docs": "https://api.python.langchain.com/en/latest/chains/langchain.chains.llm.LLMChain.html", "title": "StochasticAI"}, {"imported": "StochasticAI", "source": "langchain_community.llms", "docs": "https://api.python.langchain.com/en/latest/llms/langchain_community.llms.stochasticai.StochasticAI.html", "title": "StochasticAI"}, {"imported": "PromptTemplate", "source": "langchain_core.prompts", "docs": "https://api.python.langchain.com/en/latest/prompts/langchain_core.prompts.prompt.PromptTemplate.html", "title": "StochasticAI"}]-->
from langchain.chains import LLMChain
from langchain_community.llms import StochasticAI
from langchain_core.prompts import PromptTemplate
```


```python
template = """Question: {question}

Answer: Let's think step by step."""

prompt = PromptTemplate.from_template(template)
```


```python
llm = StochasticAI(api_url=YOUR_API_URL)
```


```python
llm_chain = LLMChain(prompt=prompt, llm=llm)
```


```python
question = "What NFL team won the Super Bowl in the year Justin Beiber was born?"

llm_chain.run(question)
```



```output
"\n\nStep 1: In 1999, the St. Louis Rams won the Super Bowl.\n\nStep 2: In 1999, Beiber was born.\n\nStep 3: The Rams were in Los Angeles at the time.\n\nStep 4: So they didn't play in the Super Bowl that year.\n"
```



## Related

- LLM [conceptual guide](/docs/concepts/#llms)
- LLM [how-to guides](/docs/how_to/#llms)