---
canonical: https://python.langchain.com/v0.2/docs/integrations/tools/jira/
custom_edit_url: https://github.com/langchain-ai/langchain/edit/master/docs/docs/integrations/tools/jira.ipynb
---

# Jira Toolkit

This notebook goes over how to use the `Jira` toolkit.

The `Jira` toolkit allows agents to interact with a given Jira instance, performing actions such as searching for issues and creating issues, the tool wraps the atlassian-python-api library, for more see: https://atlassian-python-api.readthedocs.io/jira.html

To use this tool, you must first set as environment variables:
    JIRA_API_TOKEN
    JIRA_USERNAME
    JIRA_INSTANCE_URL
    JIRA_CLOUD


```python
%pip install --upgrade --quiet  atlassian-python-api
```


```python
%pip install -qU langchain-community
```


```python
<!--IMPORTS:[{"imported": "AgentType", "source": "langchain.agents", "docs": "https://api.python.langchain.com/en/latest/agents/langchain.agents.agent_types.AgentType.html", "title": "Jira Toolkit"}, {"imported": "initialize_agent", "source": "langchain.agents", "docs": "https://api.python.langchain.com/en/latest/agents/langchain.agents.initialize.initialize_agent.html", "title": "Jira Toolkit"}, {"imported": "JiraToolkit", "source": "langchain_community.agent_toolkits.jira.toolkit", "docs": "https://api.python.langchain.com/en/latest/agent_toolkits/langchain_community.agent_toolkits.jira.toolkit.JiraToolkit.html", "title": "Jira Toolkit"}, {"imported": "JiraAPIWrapper", "source": "langchain_community.utilities.jira", "docs": "https://api.python.langchain.com/en/latest/utilities/langchain_community.utilities.jira.JiraAPIWrapper.html", "title": "Jira Toolkit"}, {"imported": "OpenAI", "source": "langchain_openai", "docs": "https://api.python.langchain.com/en/latest/llms/langchain_openai.llms.base.OpenAI.html", "title": "Jira Toolkit"}]-->
import os

from langchain.agents import AgentType, initialize_agent
from langchain_community.agent_toolkits.jira.toolkit import JiraToolkit
from langchain_community.utilities.jira import JiraAPIWrapper
from langchain_openai import OpenAI
```


```python
os.environ["JIRA_API_TOKEN"] = "abc"
os.environ["JIRA_USERNAME"] = "123"
os.environ["JIRA_INSTANCE_URL"] = "https://jira.atlassian.com"
os.environ["OPENAI_API_KEY"] = "xyz"
os.environ["JIRA_CLOUD"] = "True"
```


```python
llm = OpenAI(temperature=0)
jira = JiraAPIWrapper()
toolkit = JiraToolkit.from_jira_api_wrapper(jira)
agent = initialize_agent(
    toolkit.get_tools(), llm, agent=AgentType.ZERO_SHOT_REACT_DESCRIPTION, verbose=True
)
```


```python
agent.run("make a new issue in project PW to remind me to make more fried rice")
```
```output


[1m> Entering new AgentExecutor chain...[0m
[32;1m[1;3m I need to create an issue in project PW
Action: Create Issue
Action Input: {"summary": "Make more fried rice", "description": "Reminder to make more fried rice", "issuetype": {"name": "Task"}, "priority": {"name": "Low"}, "project": {"key": "PW"}}[0m
Observation: [38;5;200m[1;3mNone[0m
Thought:[32;1m[1;3m I now know the final answer
Final Answer: A new issue has been created in project PW with the summary "Make more fried rice" and description "Reminder to make more fried rice".[0m

[1m> Finished chain.[0m
```


```output
'A new issue has been created in project PW with the summary "Make more fried rice" and description "Reminder to make more fried rice".'
```



## Related

- Tool [conceptual guide](/docs/concepts/#tools)
- Tool [how-to guides](/docs/how_to/#tools)