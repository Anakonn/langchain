---
canonical: https://python.langchain.com/v0.2/docs/integrations/tools/nasa/
custom_edit_url: https://github.com/langchain-ai/langchain/edit/master/docs/docs/integrations/tools/nasa.ipynb
---

# NASA Toolkit

This notebook shows how to use agents to interact with the NASA toolkit. The toolkit provides access to the NASA Image and Video Library API, with potential to expand and include other accessible NASA APIs in future iterations.

**Note: NASA Image and Video Library search queries can result in large responses when the number of desired media results is not specified. Consider this prior to using the agent with LLM token credits.**

## Example Use:
---
### Initializing the agent


```python
%pip install -qU langchain-community
```


```python
<!--IMPORTS:[{"imported": "AgentType", "source": "langchain.agents", "docs": "https://api.python.langchain.com/en/latest/agents/langchain.agents.agent_types.AgentType.html", "title": "NASA Toolkit"}, {"imported": "initialize_agent", "source": "langchain.agents", "docs": "https://api.python.langchain.com/en/latest/agents/langchain.agents.initialize.initialize_agent.html", "title": "NASA Toolkit"}, {"imported": "NasaToolkit", "source": "langchain_community.agent_toolkits.nasa.toolkit", "docs": "https://api.python.langchain.com/en/latest/agent_toolkits/langchain_community.agent_toolkits.nasa.toolkit.NasaToolkit.html", "title": "NASA Toolkit"}, {"imported": "NasaAPIWrapper", "source": "langchain_community.utilities.nasa", "docs": "https://api.python.langchain.com/en/latest/utilities/langchain_community.utilities.nasa.NasaAPIWrapper.html", "title": "NASA Toolkit"}, {"imported": "OpenAI", "source": "langchain_openai", "docs": "https://api.python.langchain.com/en/latest/llms/langchain_openai.llms.base.OpenAI.html", "title": "NASA Toolkit"}]-->
from langchain.agents import AgentType, initialize_agent
from langchain_community.agent_toolkits.nasa.toolkit import NasaToolkit
from langchain_community.utilities.nasa import NasaAPIWrapper
from langchain_openai import OpenAI

llm = OpenAI(temperature=0, openai_api_key="")
nasa = NasaAPIWrapper()
toolkit = NasaToolkit.from_nasa_api_wrapper(nasa)
agent = initialize_agent(
    toolkit.get_tools(), llm, agent=AgentType.ZERO_SHOT_REACT_DESCRIPTION, verbose=True
)
```

### Querying media assets


```python
agent.run(
    "Can you find three pictures of the moon published between the years 2014 and 2020?"
)
```

### Querying details about media assets


```python
output = agent.run(
    "I've just queried an image of the moon with the NASA id NHQ_2019_0311_Go Forward to the Moon."
    " Where can I find the metadata manifest for this asset?"
)
```


## Related

- Tool [conceptual guide](/docs/concepts/#tools)
- Tool [how-to guides](/docs/how_to/#tools)