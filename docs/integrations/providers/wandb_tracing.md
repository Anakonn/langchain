---
canonical: https://python.langchain.com/v0.2/docs/integrations/providers/wandb_tracing/
custom_edit_url: https://github.com/langchain-ai/langchain/edit/master/docs/docs/integrations/providers/wandb_tracing.ipynb
---

# WandB Tracing

There are two recommended ways to trace your LangChains:

1. Setting the `LANGCHAIN_WANDB_TRACING` environment variable to "true".
1. Using a context manager with tracing_enabled() to trace a particular block of code.

**Note** if the environment variable is set, all code will be traced, regardless of whether or not it's within the context manager.


```python
<!--IMPORTS:[{"imported": "wandb_tracing_enabled", "source": "langchain_community.callbacks", "docs": "https://api.python.langchain.com/en/latest/callbacks/langchain_community.callbacks.manager.wandb_tracing_enabled.html", "title": "WandB Tracing"}, {"imported": "AgentType", "source": "langchain.agents", "docs": "https://api.python.langchain.com/en/latest/agents/langchain.agents.agent_types.AgentType.html", "title": "WandB Tracing"}, {"imported": "initialize_agent", "source": "langchain.agents", "docs": "https://api.python.langchain.com/en/latest/agents/langchain.agents.initialize.initialize_agent.html", "title": "WandB Tracing"}, {"imported": "load_tools", "source": "langchain.agents", "docs": "https://api.python.langchain.com/en/latest/agent_toolkits/langchain_community.agent_toolkits.load_tools.load_tools.html", "title": "WandB Tracing"}, {"imported": "OpenAI", "source": "langchain_openai", "docs": "https://api.python.langchain.com/en/latest/llms/langchain_openai.llms.base.OpenAI.html", "title": "WandB Tracing"}]-->
import os

from langchain_community.callbacks import wandb_tracing_enabled

os.environ["LANGCHAIN_WANDB_TRACING"] = "true"

# wandb documentation to configure wandb using env variables
# https://docs.wandb.ai/guides/track/advanced/environment-variables
# here we are configuring the wandb project name
os.environ["WANDB_PROJECT"] = "langchain-tracing"

from langchain.agents import AgentType, initialize_agent, load_tools
from langchain_openai import OpenAI
```


```python
# Agent run with tracing. Ensure that OPENAI_API_KEY is set appropriately to run this example.

llm = OpenAI(temperature=0)
tools = load_tools(["llm-math"], llm=llm)
```


```python
agent = initialize_agent(
    tools, llm, agent=AgentType.ZERO_SHOT_REACT_DESCRIPTION, verbose=True
)

agent.run("What is 2 raised to .123243 power?")  # this should be traced
# A url with for the trace sesion like the following should print in your console:
# https://wandb.ai/<wandb_entity>/<wandb_project>/runs/<run_id>
# The url can be used to view the trace session in wandb.
```


```python
# Now, we unset the environment variable and use a context manager.
if "LANGCHAIN_WANDB_TRACING" in os.environ:
    del os.environ["LANGCHAIN_WANDB_TRACING"]

# enable tracing using a context manager
with wandb_tracing_enabled():
    agent.run("What is 5 raised to .123243 power?")  # this should be traced

agent.run("What is 2 raised to .123243 power?")  # this should not be traced
```
```output


[1m> Entering new AgentExecutor chain...[0m
[32;1m[1;3m I need to use a calculator to solve this.
Action: Calculator
Action Input: 5^.123243[0m
Observation: [36;1m[1;3mAnswer: 1.2193914912400514[0m
Thought:[32;1m[1;3m I now know the final answer.
Final Answer: 1.2193914912400514[0m

[1m> Finished chain.[0m


[1m> Entering new AgentExecutor chain...[0m
[32;1m[1;3m I need to use a calculator to solve this.
Action: Calculator
Action Input: 2^.123243[0m
Observation: [36;1m[1;3mAnswer: 1.0891804557407723[0m
Thought:[32;1m[1;3m I now know the final answer.
Final Answer: 1.0891804557407723[0m

[1m> Finished chain.[0m
```


```output
'1.0891804557407723'
```