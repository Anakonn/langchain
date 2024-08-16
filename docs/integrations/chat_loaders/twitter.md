---
canonical: https://python.langchain.com/v0.2/docs/integrations/chat_loaders/twitter/
custom_edit_url: https://github.com/langchain-ai/langchain/edit/master/docs/docs/integrations/chat_loaders/twitter.ipynb
---

# Twitter (via Apify)

This notebook shows how to load chat messages from Twitter to fine-tune on. We do this by utilizing Apify. 

First, use Apify to export tweets. An example


```python
<!--IMPORTS:[{"imported": "convert_message_to_dict", "source": "langchain_community.adapters.openai", "docs": "https://api.python.langchain.com/en/latest/adapters/langchain_community.adapters.openai.convert_message_to_dict.html", "title": "Twitter (via Apify)"}, {"imported": "AIMessage", "source": "langchain_core.messages", "docs": "https://api.python.langchain.com/en/latest/messages/langchain_core.messages.ai.AIMessage.html", "title": "Twitter (via Apify)"}]-->
import json

from langchain_community.adapters.openai import convert_message_to_dict
from langchain_core.messages import AIMessage
```


```python
with open("example_data/dataset_twitter-scraper_2023-08-23_22-13-19-740.json") as f:
    data = json.load(f)
```


```python
# Filter out tweets that reference other tweets, because it's a bit weird
tweets = [d["full_text"] for d in data if "t.co" not in d["full_text"]]
# Create them as AI messages
messages = [AIMessage(content=t) for t in tweets]
# Add in a system message at the start
# TODO: we could try to extract the subject from the tweets, and put that in the system message.
system_message = {"role": "system", "content": "write a tweet"}
data = [[system_message, convert_message_to_dict(m)] for m in messages]
```