---
canonical: https://python.langchain.com/v0.2/docs/integrations/chat/solar/
custom_edit_url: https://github.com/langchain-ai/langchain/edit/master/docs/docs/integrations/chat/solar.ipynb
---

```python
<!--IMPORTS:[{"imported": "SolarChat", "source": "langchain_community.chat_models.solar", "docs": "https://api.python.langchain.com/en/latest/chat_models/langchain_community.chat_models.solar.SolarChat.html", "title": "# Related"}, {"imported": "HumanMessage", "source": "langchain_core.messages", "docs": "https://api.python.langchain.com/en/latest/messages/langchain_core.messages.human.HumanMessage.html", "title": "# Related"}, {"imported": "SystemMessage", "source": "langchain_core.messages", "docs": "https://api.python.langchain.com/en/latest/messages/langchain_core.messages.system.SystemMessage.html", "title": "# Related"}]-->
import os

os.environ["SOLAR_API_KEY"] = "SOLAR_API_KEY"

from langchain_community.chat_models.solar import SolarChat
from langchain_core.messages import HumanMessage, SystemMessage

chat = SolarChat(max_tokens=1024)

messages = [
    SystemMessage(
        content="You are a helpful assistant who translates English to Korean."
    ),
    HumanMessage(
        content="Translate this sentence from English to Korean. I want to build a project of large language model."
    ),
]

chat.invoke(messages)
```



```output
AIMessage(content='저는 대형 언어 모델 프로젝트를 구축하고 싶습니다.')
```



## Related

- Chat model [conceptual guide](/docs/concepts/#chat-models)
- Chat model [how-to guides](/docs/how_to/#chat-models)