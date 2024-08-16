---
canonical: https://python.langchain.com/v0.2/docs/integrations/tools/mojeek_search/
custom_edit_url: https://github.com/langchain-ai/langchain/edit/master/docs/docs/integrations/tools/mojeek_search.ipynb
---

# Mojeek Search

The following notebook will explain how to get results using Mojeek Search. Please visit [Mojeek Website](https://www.mojeek.com/services/search/web-search-api/) to obtain an API key.


```python
<!--IMPORTS:[{"imported": "MojeekSearch", "source": "langchain_community.tools", "docs": "https://api.python.langchain.com/en/latest/tools/langchain_community.tools.mojeek_search.tool.MojeekSearch.html", "title": "Mojeek Search"}]-->
from langchain_community.tools import MojeekSearch
```


```python
api_key = "KEY"  # obtained from Mojeek Website
```


```python
search = MojeekSearch.config(api_key=api_key, search_kwargs={"t": 10})
```

In `search_kwargs` you can add any search parameter that you can find on [Mojeek Documentation](https://www.mojeek.com/support/api/search/request_parameters.html)


```python
search.run("mojeek")
```


## Related

- Tool [conceptual guide](/docs/concepts/#tools)
- Tool [how-to guides](/docs/how_to/#tools)