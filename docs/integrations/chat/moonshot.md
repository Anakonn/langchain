---
canonical: https://python.langchain.com/v0.2/docs/integrations/chat/moonshot/
custom_edit_url: https://github.com/langchain-ai/langchain/edit/master/docs/docs/integrations/chat/moonshot.ipynb
sidebar_label: Moonshot
---

# MoonshotChat

[Moonshot](https://platform.moonshot.cn/) is a Chinese startup that provides LLM service for companies and individuals.

This example goes over how to use LangChain to interact with Moonshot Inference for Chat.


```python
import os

# Generate your api key from: https://platform.moonshot.cn/console/api-keys
os.environ["MOONSHOT_API_KEY"] = "MOONSHOT_API_KEY"
```


```python
<!--IMPORTS:[{"imported": "MoonshotChat", "source": "langchain_community.chat_models.moonshot", "docs": "https://api.python.langchain.com/en/latest/chat_models/langchain_community.chat_models.moonshot.MoonshotChat.html", "title": "MoonshotChat"}, {"imported": "HumanMessage", "source": "langchain_core.messages", "docs": "https://api.python.langchain.com/en/latest/messages/langchain_core.messages.human.HumanMessage.html", "title": "MoonshotChat"}, {"imported": "SystemMessage", "source": "langchain_core.messages", "docs": "https://api.python.langchain.com/en/latest/messages/langchain_core.messages.system.SystemMessage.html", "title": "MoonshotChat"}]-->
from langchain_community.chat_models.moonshot import MoonshotChat
from langchain_core.messages import HumanMessage, SystemMessage
```


```python
chat = MoonshotChat()
# or use a specific model
# Available models: https://platform.moonshot.cn/docs
# chat = MoonshotChat(model="moonshot-v1-128k")
```


```python
messages = [
    SystemMessage(
        content="You are a helpful assistant that translates English to French."
    ),
    HumanMessage(
        content="Translate this sentence from English to French. I love programming."
    ),
]

chat.invoke(messages)
```


## Related

- Chat model [conceptual guide](/docs/concepts/#chat-models)
- Chat model [how-to guides](/docs/how_to/#chat-models)