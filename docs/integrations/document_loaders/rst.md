---
canonical: https://python.langchain.com/v0.2/docs/integrations/document_loaders/rst/
custom_edit_url: https://github.com/langchain-ai/langchain/edit/master/docs/docs/integrations/document_loaders/rst.ipynb
---

# RST

>A [reStructured Text (RST)](https://en.wikipedia.org/wiki/ReStructuredText) file is a file format for textual data used primarily in the Python programming language community for technical documentation.

## `UnstructuredRSTLoader`

You can load data from RST files with `UnstructuredRSTLoader` using the following workflow.


```python
<!--IMPORTS:[{"imported": "UnstructuredRSTLoader", "source": "langchain_community.document_loaders", "docs": "https://api.python.langchain.com/en/latest/document_loaders/langchain_community.document_loaders.rst.UnstructuredRSTLoader.html", "title": "RST"}]-->
from langchain_community.document_loaders import UnstructuredRSTLoader

loader = UnstructuredRSTLoader(file_path="./example_data/README.rst", mode="elements")
docs = loader.load()

print(docs[0])
```
```output
page_content='Example Docs' metadata={'source': './example_data/README.rst', 'category_depth': 0, 'last_modified': '2023-12-19T13:42:18', 'languages': ['eng'], 'filetype': 'text/x-rst', 'file_directory': './example_data', 'filename': 'README.rst', 'category': 'Title'}
```

## Related

- Document loader [conceptual guide](/docs/concepts/#document-loaders)
- Document loader [how-to guides](/docs/how_to/#document-loaders)