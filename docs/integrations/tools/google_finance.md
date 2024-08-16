---
canonical: https://python.langchain.com/v0.2/docs/integrations/tools/google_finance/
custom_edit_url: https://github.com/langchain-ai/langchain/edit/master/docs/docs/integrations/tools/google_finance.ipynb
---

# Google Finance

This notebook goes over how to use the Google Finance Tool to get information from the Google Finance page

To get an SerpApi key key, sign up at: https://serpapi.com/users/sign_up.

Then install google-search-results with the command: 

pip install google-search-results

Then set the environment variable SERPAPI_API_KEY to your SerpApi key

Or pass the key in as a argument to the wrapper serp_api_key="your secret key"

Use the Tool


```python
%pip install --upgrade --quiet  google-search-results langchain-community
```


```python
<!--IMPORTS:[{"imported": "GoogleFinanceQueryRun", "source": "langchain_community.tools.google_finance", "docs": "https://api.python.langchain.com/en/latest/tools/langchain_community.tools.google_finance.tool.GoogleFinanceQueryRun.html", "title": "Google Finance"}, {"imported": "GoogleFinanceAPIWrapper", "source": "langchain_community.utilities.google_finance", "docs": "https://api.python.langchain.com/en/latest/utilities/langchain_community.utilities.google_finance.GoogleFinanceAPIWrapper.html", "title": "Google Finance"}]-->
import os

from langchain_community.tools.google_finance import GoogleFinanceQueryRun
from langchain_community.utilities.google_finance import GoogleFinanceAPIWrapper

os.environ["SERPAPI_API_KEY"] = ""
tool = GoogleFinanceQueryRun(api_wrapper=GoogleFinanceAPIWrapper())
```


```python
tool.run("Google")
```

Using it with Langchain


```python
<!--IMPORTS:[{"imported": "AgentType", "source": "langchain.agents", "docs": "https://api.python.langchain.com/en/latest/agents/langchain.agents.agent_types.AgentType.html", "title": "Google Finance"}, {"imported": "initialize_agent", "source": "langchain.agents", "docs": "https://api.python.langchain.com/en/latest/agents/langchain.agents.initialize.initialize_agent.html", "title": "Google Finance"}, {"imported": "load_tools", "source": "langchain.agents", "docs": "https://api.python.langchain.com/en/latest/agent_toolkits/langchain_community.agent_toolkits.load_tools.load_tools.html", "title": "Google Finance"}, {"imported": "OpenAI", "source": "langchain_openai", "docs": "https://api.python.langchain.com/en/latest/llms/langchain_openai.llms.base.OpenAI.html", "title": "Google Finance"}]-->
import os

from langchain.agents import AgentType, initialize_agent, load_tools
from langchain_openai import OpenAI

os.environ["OPENAI_API_KEY"] = ""
os.environ["SERP_API_KEY"] = ""
llm = OpenAI()
tools = load_tools(["google-scholar", "google-finance"], llm=llm)
agent = initialize_agent(
    tools, llm, agent=AgentType.ZERO_SHOT_REACT_DESCRIPTION, verbose=True
)
agent.run("what is google's stock")
```


## Related

- Tool [conceptual guide](/docs/concepts/#tools)
- Tool [how-to guides](/docs/how_to/#tools)