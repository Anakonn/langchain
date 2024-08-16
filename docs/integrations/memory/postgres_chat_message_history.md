---
canonical: https://python.langchain.com/v0.2/docs/integrations/memory/postgres_chat_message_history/
custom_edit_url: https://github.com/langchain-ai/langchain/edit/master/docs/docs/integrations/memory/postgres_chat_message_history.ipynb
---

# Postgres

>[PostgreSQL](https://en.wikipedia.org/wiki/PostgreSQL) also known as `Postgres`, is a free and open-source relational database management system (RDBMS) emphasizing extensibility and SQL compliance.

This notebook goes over how to use Postgres to store chat message history.


```python
<!--IMPORTS:[{"imported": "PostgresChatMessageHistory", "source": "langchain_community.chat_message_histories", "docs": "https://api.python.langchain.com/en/latest/chat_message_histories/langchain_community.chat_message_histories.postgres.PostgresChatMessageHistory.html", "title": "Postgres"}]-->
from langchain_community.chat_message_histories import (
    PostgresChatMessageHistory,
)

history = PostgresChatMessageHistory(
    connection_string="postgresql://postgres:mypassword@localhost/chat_history",
    session_id="foo",
)

history.add_user_message("hi!")

history.add_ai_message("whats up?")
```


```python
history.messages
```