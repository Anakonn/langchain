---
canonical: https://python.langchain.com/v0.2/docs/integrations/document_loaders/readthedocs_documentation/
custom_edit_url: https://github.com/langchain-ai/langchain/edit/master/docs/docs/integrations/document_loaders/readthedocs_documentation.ipynb
---

# ReadTheDocs Documentation

>[Read the Docs](https://readthedocs.org/) is an open-sourced free software documentation hosting platform. It generates documentation written with the `Sphinx` documentation generator.

This notebook covers how to load content from HTML that was generated as part of a `Read-The-Docs` build.

For an example of this in the wild, see [here](https://github.com/langchain-ai/chat-langchain).

This assumes that the HTML has already been scraped into a folder. This can be done by uncommenting and running the following command


```python
%pip install --upgrade --quiet  beautifulsoup4
```


```python
#!wget -r -A.html -P rtdocs https://python.langchain.com/en/latest/
```


```python
<!--IMPORTS:[{"imported": "ReadTheDocsLoader", "source": "langchain_community.document_loaders", "docs": "https://api.python.langchain.com/en/latest/document_loaders/langchain_community.document_loaders.readthedocs.ReadTheDocsLoader.html", "title": "ReadTheDocs Documentation"}]-->
from langchain_community.document_loaders import ReadTheDocsLoader
```


```python
loader = ReadTheDocsLoader("rtdocs", features="html.parser")
```


```python
docs = loader.load()
```


## Related

- Document loader [conceptual guide](/docs/concepts/#document-loaders)
- Document loader [how-to guides](/docs/how_to/#document-loaders)