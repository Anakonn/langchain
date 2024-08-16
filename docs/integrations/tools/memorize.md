---
canonical: https://python.langchain.com/v0.2/docs/integrations/tools/memorize/
custom_edit_url: https://github.com/langchain-ai/langchain/edit/master/docs/docs/integrations/tools/memorize.ipynb
---

# Memorize

Fine-tuning LLM itself to memorize information using unsupervised learning.

This tool requires LLMs that support fine-tuning. Currently, only `langchain.llms import GradientLLM` is supported.

## Imports


```python
<!--IMPORTS:[{"imported": "AgentExecutor", "source": "langchain.agents", "docs": "https://api.python.langchain.com/en/latest/agents/langchain.agents.agent.AgentExecutor.html", "title": "Memorize"}, {"imported": "AgentType", "source": "langchain.agents", "docs": "https://api.python.langchain.com/en/latest/agents/langchain.agents.agent_types.AgentType.html", "title": "Memorize"}, {"imported": "initialize_agent", "source": "langchain.agents", "docs": "https://api.python.langchain.com/en/latest/agents/langchain.agents.initialize.initialize_agent.html", "title": "Memorize"}, {"imported": "load_tools", "source": "langchain.agents", "docs": "https://api.python.langchain.com/en/latest/agent_toolkits/langchain_community.agent_toolkits.load_tools.load_tools.html", "title": "Memorize"}, {"imported": "LLMChain", "source": "langchain.chains", "docs": "https://api.python.langchain.com/en/latest/chains/langchain.chains.llm.LLMChain.html", "title": "Memorize"}, {"imported": "ConversationBufferMemory", "source": "langchain.memory", "docs": "https://api.python.langchain.com/en/latest/memory/langchain.memory.buffer.ConversationBufferMemory.html", "title": "Memorize"}, {"imported": "GradientLLM", "source": "langchain_community.llms", "docs": "https://api.python.langchain.com/en/latest/llms/langchain_community.llms.gradient_ai.GradientLLM.html", "title": "Memorize"}]-->
import os

from langchain.agents import AgentExecutor, AgentType, initialize_agent, load_tools
from langchain.chains import LLMChain
from langchain.memory import ConversationBufferMemory
from langchain_community.llms import GradientLLM
```

## Set the Environment API Key
Make sure to get your API key from Gradient AI. You are given $10 in free credits to test and fine-tune different models.


```python
from getpass import getpass

if not os.environ.get("GRADIENT_ACCESS_TOKEN", None):
    # Access token under https://auth.gradient.ai/select-workspace
    os.environ["GRADIENT_ACCESS_TOKEN"] = getpass("gradient.ai access token:")
if not os.environ.get("GRADIENT_WORKSPACE_ID", None):
    # `ID` listed in `$ gradient workspace list`
    # also displayed after login at at https://auth.gradient.ai/select-workspace
    os.environ["GRADIENT_WORKSPACE_ID"] = getpass("gradient.ai workspace id:")
if not os.environ.get("GRADIENT_MODEL_ADAPTER_ID", None):
    # `ID` listed in `$ gradient model list --workspace-id "$GRADIENT_WORKSPACE_ID"`
    os.environ["GRADIENT_MODEL_ID"] = getpass("gradient.ai model id:")
```

Optional: Validate your Environment variables ```GRADIENT_ACCESS_TOKEN``` and ```GRADIENT_WORKSPACE_ID``` to get currently deployed models.

## Create the `GradientLLM` instance
You can specify different parameters such as the model name, max tokens generated, temperature, etc.


```python
llm = GradientLLM(
    model_id=os.environ["GRADIENT_MODEL_ID"],
    # # optional: set new credentials, they default to environment variables
    # gradient_workspace_id=os.environ["GRADIENT_WORKSPACE_ID"],
    # gradient_access_token=os.environ["GRADIENT_ACCESS_TOKEN"],
)
```

## Load tools


```python
tools = load_tools(["memorize"], llm=llm)
```

## Initiate the Agent


```python
agent = initialize_agent(
    tools,
    llm,
    agent=AgentType.ZERO_SHOT_REACT_DESCRIPTION,
    verbose=True,
    # memory=ConversationBufferMemory(memory_key="chat_history", return_messages=True),
)
```

## Run the agent
Ask the agent to memorize a piece of text.


```python
agent.run(
    "Please remember the fact in detail:\nWith astonishing dexterity, Zara Tubikova set a world record by solving a 4x4 Rubik's Cube variation blindfolded in under 20 seconds, employing only their feet."
)
```
```output


[1m> Entering new AgentExecutor chain...[0m
[32;1m[1;3mI should memorize this fact.
Action: Memorize
Action Input: Zara T[0m
Observation: [36;1m[1;3mTrain complete. Loss: 1.6853971333333335[0m
Thought:[32;1m[1;3mI now know the final answer.
Final Answer: Zara Tubikova set a world[0m

[1m> Finished chain.[0m
```


```output
'Zara Tubikova set a world'
```



## Related

- Tool [conceptual guide](/docs/concepts/#tools)
- Tool [how-to guides](/docs/how_to/#tools)