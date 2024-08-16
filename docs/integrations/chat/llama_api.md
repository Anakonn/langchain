---
canonical: https://python.langchain.com/v0.2/docs/integrations/chat/llama_api/
custom_edit_url: https://github.com/langchain-ai/langchain/edit/master/docs/docs/integrations/chat/llama_api.ipynb
sidebar_label: Llama API
---

# ChatLlamaAPI

This notebook shows how to use LangChain with [LlamaAPI](https://llama-api.com/) - a hosted version of Llama2 that adds in support for function calling.

%pip install --upgrade --quiet  llamaapi


```python
from llamaapi import LlamaAPI

# Replace 'Your_API_Token' with your actual API token
llama = LlamaAPI("Your_API_Token")
```


```python
<!--IMPORTS:[{"imported": "ChatLlamaAPI", "source": "langchain_experimental.llms", "docs": "https://api.python.langchain.com/en/latest/llms/langchain_experimental.llms.llamaapi.ChatLlamaAPI.html", "title": "ChatLlamaAPI"}]-->
from langchain_experimental.llms import ChatLlamaAPI
```
```output
/Users/harrisonchase/.pyenv/versions/3.9.1/envs/langchain/lib/python3.9/site-packages/deeplake/util/check_latest_version.py:32: UserWarning: A newer version of deeplake (3.6.12) is available. It's recommended that you update to the latest version using `pip install -U deeplake`.
  warnings.warn(
```

```python
model = ChatLlamaAPI(client=llama)
```


```python
<!--IMPORTS:[{"imported": "create_tagging_chain", "source": "langchain.chains", "docs": "https://api.python.langchain.com/en/latest/chains/langchain.chains.openai_functions.tagging.create_tagging_chain.html", "title": "ChatLlamaAPI"}]-->
from langchain.chains import create_tagging_chain

schema = {
    "properties": {
        "sentiment": {
            "type": "string",
            "description": "the sentiment encountered in the passage",
        },
        "aggressiveness": {
            "type": "integer",
            "description": "a 0-10 score of how aggressive the passage is",
        },
        "language": {"type": "string", "description": "the language of the passage"},
    }
}

chain = create_tagging_chain(schema, model)
```


```python
chain.run("give me your money")
```



```output
{'sentiment': 'aggressive', 'aggressiveness': 8, 'language': 'english'}
```



## Related

- Chat model [conceptual guide](/docs/concepts/#chat-models)
- Chat model [how-to guides](/docs/how_to/#chat-models)