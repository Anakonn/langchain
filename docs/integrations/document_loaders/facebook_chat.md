---
canonical: https://python.langchain.com/v0.2/docs/integrations/document_loaders/facebook_chat/
custom_edit_url: https://github.com/langchain-ai/langchain/edit/master/docs/docs/integrations/document_loaders/facebook_chat.ipynb
---

# Facebook Chat

>[Messenger](https://en.wikipedia.org/wiki/Messenger_(software)) is an American proprietary instant messaging app and platform developed by `Meta Platforms`. Originally developed as `Facebook Chat` in 2008, the company revamped its messaging service in 2010.

This notebook covers how to load data from the [Facebook Chats](https://www.facebook.com/business/help/1646890868956360) into a format that can be ingested into LangChain.


```python
# pip install pandas
```


```python
<!--IMPORTS:[{"imported": "FacebookChatLoader", "source": "langchain_community.document_loaders", "docs": "https://api.python.langchain.com/en/latest/document_loaders/langchain_community.document_loaders.facebook_chat.FacebookChatLoader.html", "title": "Facebook Chat"}]-->
from langchain_community.document_loaders import FacebookChatLoader
```


```python
loader = FacebookChatLoader("example_data/facebook_chat.json")
```


```python
loader.load()
```



```output
[Document(page_content='User 2 on 2023-02-05 03:46:11: Bye!\n\nUser 1 on 2023-02-05 03:43:55: Oh no worries! Bye\n\nUser 2 on 2023-02-05 03:24:37: No Im sorry it was my mistake, the blue one is not for sale\n\nUser 1 on 2023-02-05 03:05:40: I thought you were selling the blue one!\n\nUser 1 on 2023-02-05 03:05:09: Im not interested in this bag. Im interested in the blue one!\n\nUser 2 on 2023-02-05 03:04:28: Here is $129\n\nUser 2 on 2023-02-05 03:04:05: Online is at least $100\n\nUser 1 on 2023-02-05 02:59:59: How much do you want?\n\nUser 2 on 2023-02-04 22:17:56: Goodmorning! $50 is too low.\n\nUser 1 on 2023-02-04 14:17:02: Hi! Im interested in your bag. Im offering $50. Let me know if you are interested. Thanks!\n\n', metadata={'source': 'example_data/facebook_chat.json'})]
```



## Related

- Document loader [conceptual guide](/docs/concepts/#document-loaders)
- Document loader [how-to guides](/docs/how_to/#document-loaders)