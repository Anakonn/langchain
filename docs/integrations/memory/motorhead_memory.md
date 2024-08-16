---
canonical: https://python.langchain.com/v0.2/docs/integrations/memory/motorhead_memory/
custom_edit_url: https://github.com/langchain-ai/langchain/edit/master/docs/docs/integrations/memory/motorhead_memory.ipynb
---

# Motörhead

>[Motörhead](https://github.com/getmetal/motorhead) is a memory server implemented in Rust. It automatically handles incremental summarization in the background and allows for stateless applications.

## Setup

See instructions at [Motörhead](https://github.com/getmetal/motorhead) for running the server locally.


```python
<!--IMPORTS:[{"imported": "MotorheadMemory", "source": "langchain_community.memory.motorhead_memory", "docs": "https://api.python.langchain.com/en/latest/memory/langchain_community.memory.motorhead_memory.MotorheadMemory.html", "title": "Mot\u00f6rhead"}]-->
from langchain_community.memory.motorhead_memory import MotorheadMemory
```

## Example


```python
<!--IMPORTS:[{"imported": "LLMChain", "source": "langchain.chains", "docs": "https://api.python.langchain.com/en/latest/chains/langchain.chains.llm.LLMChain.html", "title": "Mot\u00f6rhead"}, {"imported": "PromptTemplate", "source": "langchain_core.prompts", "docs": "https://api.python.langchain.com/en/latest/prompts/langchain_core.prompts.prompt.PromptTemplate.html", "title": "Mot\u00f6rhead"}, {"imported": "OpenAI", "source": "langchain_openai", "docs": "https://api.python.langchain.com/en/latest/llms/langchain_openai.llms.base.OpenAI.html", "title": "Mot\u00f6rhead"}]-->
from langchain.chains import LLMChain
from langchain_core.prompts import PromptTemplate
from langchain_openai import OpenAI

template = """You are a chatbot having a conversation with a human.

{chat_history}
Human: {human_input}
AI:"""

prompt = PromptTemplate(
    input_variables=["chat_history", "human_input"], template=template
)
memory = MotorheadMemory(
    session_id="testing-1", url="http://localhost:8080", memory_key="chat_history"
)

await memory.init()
# loads previous state from Motörhead 🤘

llm_chain = LLMChain(
    llm=OpenAI(),
    prompt=prompt,
    verbose=True,
    memory=memory,
)
```


```python
llm_chain.run("hi im bob")
```
```output


[1m> Entering new LLMChain chain...[0m
Prompt after formatting:
[32;1m[1;3mYou are a chatbot having a conversation with a human.


Human: hi im bob
AI:[0m

[1m> Finished chain.[0m
```


```output
' Hi Bob, nice to meet you! How are you doing today?'
```



```python
llm_chain.run("whats my name?")
```
```output


[1m> Entering new LLMChain chain...[0m
Prompt after formatting:
[32;1m[1;3mYou are a chatbot having a conversation with a human.

Human: hi im bob
AI:  Hi Bob, nice to meet you! How are you doing today?
Human: whats my name?
AI:[0m

[1m> Finished chain.[0m
```


```output
' You said your name is Bob. Is that correct?'
```



```python
llm_chain.run("whats for dinner?")
```
```output


[1m> Entering new LLMChain chain...[0m
Prompt after formatting:
[32;1m[1;3mYou are a chatbot having a conversation with a human.

Human: hi im bob
AI:  Hi Bob, nice to meet you! How are you doing today?
Human: whats my name?
AI:  You said your name is Bob. Is that correct?
Human: whats for dinner?
AI:[0m

[1m> Finished chain.[0m
```


```output
"  I'm sorry, I'm not sure what you're asking. Could you please rephrase your question?"
```