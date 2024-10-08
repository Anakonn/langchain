---
canonical: https://python.langchain.com/v0.2/docs/integrations/llms/moonshot/
custom_edit_url: https://github.com/langchain-ai/langchain/edit/master/docs/docs/integrations/llms/moonshot.ipynb
---

# MoonshotChat

[Moonshot](https://platform.moonshot.cn/) is a Chinese startup that provides LLM service for companies and individuals.

This example goes over how to use LangChain to interact with Moonshot.


```python
<!--IMPORTS:[{"imported": "Moonshot", "source": "langchain_community.llms.moonshot", "docs": "https://api.python.langchain.com/en/latest/llms/langchain_community.llms.moonshot.Moonshot.html", "title": "MoonshotChat"}]-->
from langchain_community.llms.moonshot import Moonshot
```


```python
import os

# Generate your api key from: https://platform.moonshot.cn/console/api-keys
os.environ["MOONSHOT_API_KEY"] = "MOONSHOT_API_KEY"
```


```python
llm = Moonshot()
# or use a specific model
# Available models: https://platform.moonshot.cn/docs
# llm = Moonshot(model="moonshot-v1-128k")
```


```python
# Prompt the model
llm.invoke("What is the difference between panda and bear?")
```


## Related

- LLM [conceptual guide](/docs/concepts/#llms)
- LLM [how-to guides](/docs/how_to/#llms)