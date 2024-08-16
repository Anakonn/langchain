---
canonical: https://python.langchain.com/v0.2/docs/integrations/document_loaders/merge_doc/
custom_edit_url: https://github.com/langchain-ai/langchain/edit/master/docs/docs/integrations/document_loaders/merge_doc.ipynb
---

# Merge Documents Loader

Merge the documents returned from a set of specified data loaders.


```python
<!--IMPORTS:[{"imported": "WebBaseLoader", "source": "langchain_community.document_loaders", "docs": "https://api.python.langchain.com/en/latest/document_loaders/langchain_community.document_loaders.web_base.WebBaseLoader.html", "title": "Merge Documents Loader"}]-->
from langchain_community.document_loaders import WebBaseLoader

loader_web = WebBaseLoader(
    "https://github.com/basecamp/handbook/blob/master/37signals-is-you.md"
)
```


```python
<!--IMPORTS:[{"imported": "PyPDFLoader", "source": "langchain_community.document_loaders", "docs": "https://api.python.langchain.com/en/latest/document_loaders/langchain_community.document_loaders.pdf.PyPDFLoader.html", "title": "Merge Documents Loader"}]-->
from langchain_community.document_loaders import PyPDFLoader

loader_pdf = PyPDFLoader("../MachineLearning-Lecture01.pdf")
```


```python
<!--IMPORTS:[{"imported": "MergedDataLoader", "source": "langchain_community.document_loaders.merge", "docs": "https://api.python.langchain.com/en/latest/document_loaders/langchain_community.document_loaders.merge.MergedDataLoader.html", "title": "Merge Documents Loader"}]-->
from langchain_community.document_loaders.merge import MergedDataLoader

loader_all = MergedDataLoader(loaders=[loader_web, loader_pdf])
```


```python
docs_all = loader_all.load()
```


```python
len(docs_all)
```



```output
23
```



## Related

- Document loader [conceptual guide](/docs/concepts/#document-loaders)
- Document loader [how-to guides](/docs/how_to/#document-loaders)