---
canonical: https://python.langchain.com/v0.2/docs/integrations/memory/singlestoredb_chat_message_history/
custom_edit_url: https://github.com/langchain-ai/langchain/edit/master/docs/docs/integrations/memory/singlestoredb_chat_message_history.ipynb
---

# SingleStoreDB

This notebook goes over how to use SingleStoreDB to store chat message history.


```python
<!--IMPORTS:[{"imported": "SingleStoreDBChatMessageHistory", "source": "langchain_community.chat_message_histories", "docs": "https://api.python.langchain.com/en/latest/chat_message_histories/langchain_community.chat_message_histories.singlestoredb.SingleStoreDBChatMessageHistory.html", "title": "SingleStoreDB"}]-->
from langchain_community.chat_message_histories import (
    SingleStoreDBChatMessageHistory,
)

history = SingleStoreDBChatMessageHistory(
    session_id="foo", host="root:pass@localhost:3306/db"
)

history.add_user_message("hi!")

history.add_ai_message("whats up?")
```


```python
history.messages
```