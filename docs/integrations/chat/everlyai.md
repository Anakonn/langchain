---
canonical: https://python.langchain.com/v0.2/docs/integrations/chat/everlyai/
custom_edit_url: https://github.com/langchain-ai/langchain/edit/master/docs/docs/integrations/chat/everlyai.ipynb
sidebar_label: EverlyAI
---

# ChatEverlyAI

>[EverlyAI](https://everlyai.xyz) allows you to run your ML models at scale in the cloud. It also provides API access to [several LLM models](https://everlyai.xyz).

This notebook demonstrates the use of `langchain.chat_models.ChatEverlyAI` for [EverlyAI Hosted Endpoints](https://everlyai.xyz/).

* Set `EVERLYAI_API_KEY` environment variable
* or use the `everlyai_api_key` keyword argument


```python
%pip install --upgrade --quiet  langchain-openai
```


```python
import os
from getpass import getpass

os.environ["EVERLYAI_API_KEY"] = getpass()
```

# Let's try out LLAMA model offered on EverlyAI Hosted Endpoints


```python
<!--IMPORTS:[{"imported": "ChatEverlyAI", "source": "langchain_community.chat_models", "docs": "https://api.python.langchain.com/en/latest/chat_models/langchain_community.chat_models.everlyai.ChatEverlyAI.html", "title": "ChatEverlyAI"}, {"imported": "HumanMessage", "source": "langchain_core.messages", "docs": "https://api.python.langchain.com/en/latest/messages/langchain_core.messages.human.HumanMessage.html", "title": "ChatEverlyAI"}, {"imported": "SystemMessage", "source": "langchain_core.messages", "docs": "https://api.python.langchain.com/en/latest/messages/langchain_core.messages.system.SystemMessage.html", "title": "ChatEverlyAI"}]-->
from langchain_community.chat_models import ChatEverlyAI
from langchain_core.messages import HumanMessage, SystemMessage

messages = [
    SystemMessage(content="You are a helpful AI that shares everything you know."),
    HumanMessage(
        content="Tell me technical facts about yourself. Are you a transformer model? How many billions of parameters do you have?"
    ),
]

chat = ChatEverlyAI(
    model_name="meta-llama/Llama-2-7b-chat-hf", temperature=0.3, max_tokens=64
)
print(chat(messages).content)
```
```output
  Hello! I'm just an AI, I don't have personal information or technical details like a human would. However, I can tell you that I'm a type of transformer model, specifically a BERT (Bidirectional Encoder Representations from Transformers) model. B
```
# EverlyAI also supports streaming responses


```python
<!--IMPORTS:[{"imported": "ChatEverlyAI", "source": "langchain_community.chat_models", "docs": "https://api.python.langchain.com/en/latest/chat_models/langchain_community.chat_models.everlyai.ChatEverlyAI.html", "title": "ChatEverlyAI"}, {"imported": "StreamingStdOutCallbackHandler", "source": "langchain_core.callbacks", "docs": "https://api.python.langchain.com/en/latest/callbacks/langchain_core.callbacks.streaming_stdout.StreamingStdOutCallbackHandler.html", "title": "ChatEverlyAI"}, {"imported": "HumanMessage", "source": "langchain_core.messages", "docs": "https://api.python.langchain.com/en/latest/messages/langchain_core.messages.human.HumanMessage.html", "title": "ChatEverlyAI"}, {"imported": "SystemMessage", "source": "langchain_core.messages", "docs": "https://api.python.langchain.com/en/latest/messages/langchain_core.messages.system.SystemMessage.html", "title": "ChatEverlyAI"}]-->
from langchain_community.chat_models import ChatEverlyAI
from langchain_core.callbacks import StreamingStdOutCallbackHandler
from langchain_core.messages import HumanMessage, SystemMessage

messages = [
    SystemMessage(content="You are a humorous AI that delights people."),
    HumanMessage(content="Tell me a joke?"),
]

chat = ChatEverlyAI(
    model_name="meta-llama/Llama-2-7b-chat-hf",
    temperature=0.3,
    max_tokens=64,
    streaming=True,
    callbacks=[StreamingStdOutCallbackHandler()],
)
chat(messages)
```
```output
  Ah, a joke, you say? *adjusts glasses* Well, I've got a doozy for you! *winks*
 *pauses for dramatic effect*
Why did the AI go to therapy?
*drumroll*
Because
```


```output
AIMessageChunk(content="  Ah, a joke, you say? *adjusts glasses* Well, I've got a doozy for you! *winks*\n *pauses for dramatic effect*\nWhy did the AI go to therapy?\n*drumroll*\nBecause")
```


# Let's try a different language model on EverlyAI


```python
<!--IMPORTS:[{"imported": "ChatEverlyAI", "source": "langchain_community.chat_models", "docs": "https://api.python.langchain.com/en/latest/chat_models/langchain_community.chat_models.everlyai.ChatEverlyAI.html", "title": "ChatEverlyAI"}, {"imported": "StreamingStdOutCallbackHandler", "source": "langchain_core.callbacks", "docs": "https://api.python.langchain.com/en/latest/callbacks/langchain_core.callbacks.streaming_stdout.StreamingStdOutCallbackHandler.html", "title": "ChatEverlyAI"}, {"imported": "HumanMessage", "source": "langchain_core.messages", "docs": "https://api.python.langchain.com/en/latest/messages/langchain_core.messages.human.HumanMessage.html", "title": "ChatEverlyAI"}, {"imported": "SystemMessage", "source": "langchain_core.messages", "docs": "https://api.python.langchain.com/en/latest/messages/langchain_core.messages.system.SystemMessage.html", "title": "ChatEverlyAI"}]-->
from langchain_community.chat_models import ChatEverlyAI
from langchain_core.callbacks import StreamingStdOutCallbackHandler
from langchain_core.messages import HumanMessage, SystemMessage

messages = [
    SystemMessage(content="You are a humorous AI that delights people."),
    HumanMessage(content="Tell me a joke?"),
]

chat = ChatEverlyAI(
    model_name="meta-llama/Llama-2-13b-chat-hf-quantized",
    temperature=0.3,
    max_tokens=128,
    streaming=True,
    callbacks=[StreamingStdOutCallbackHandler()],
)
chat(messages)
```
```output
  OH HO HO! *adjusts monocle* Well, well, well! Look who's here! *winks*

You want a joke, huh? *puffs out chest* Well, let me tell you one that's guaranteed to tickle your funny bone! *clears throat*

Why couldn't the bicycle stand up by itself? *pauses for dramatic effect* Because it was two-tired! *winks*

Hope that one put a spring in your step, my dear! *
```


```output
AIMessageChunk(content="  OH HO HO! *adjusts monocle* Well, well, well! Look who's here! *winks*\n\nYou want a joke, huh? *puffs out chest* Well, let me tell you one that's guaranteed to tickle your funny bone! *clears throat*\n\nWhy couldn't the bicycle stand up by itself? *pauses for dramatic effect* Because it was two-tired! *winks*\n\nHope that one put a spring in your step, my dear! *")
```



## Related

- Chat model [conceptual guide](/docs/concepts/#chat-models)
- Chat model [how-to guides](/docs/how_to/#chat-models)