---
canonical: https://python.langchain.com/v0.2/docs/integrations/memory/neo4j_chat_message_history/
custom_edit_url: https://github.com/langchain-ai/langchain/edit/master/docs/docs/integrations/memory/neo4j_chat_message_history.ipynb
---

# Neo4j

[Neo4j](https://en.wikipedia.org/wiki/Neo4j) is an open-source graph database management system, renowned for its efficient management of highly connected data. Unlike traditional databases that store data in tables, Neo4j uses a graph structure with nodes, edges, and properties to represent and store data. This design allows for high-performance queries on complex data relationships.

This notebook goes over how to use `Neo4j` to store chat message history.


```python
<!--IMPORTS:[{"imported": "Neo4jChatMessageHistory", "source": "langchain_community.chat_message_histories", "docs": "https://api.python.langchain.com/en/latest/chat_message_histories/langchain_community.chat_message_histories.neo4j.Neo4jChatMessageHistory.html", "title": "Neo4j"}]-->
from langchain_community.chat_message_histories import Neo4jChatMessageHistory

history = Neo4jChatMessageHistory(
    url="bolt://localhost:7687",
    username="neo4j",
    password="password",
    session_id="session_id_1",
)

history.add_user_message("hi!")

history.add_ai_message("whats up?")
```


```python
history.messages
```