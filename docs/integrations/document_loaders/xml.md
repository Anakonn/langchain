---
canonical: https://python.langchain.com/v0.2/docs/integrations/document_loaders/xml/
custom_edit_url: https://github.com/langchain-ai/langchain/edit/master/docs/docs/integrations/document_loaders/xml.ipynb
---

# XML

The `UnstructuredXMLLoader` is used to load `XML` files. The loader works with `.xml` files. The page content will be the text extracted from the XML tags.


```python
<!--IMPORTS:[{"imported": "UnstructuredXMLLoader", "source": "langchain_community.document_loaders", "docs": "https://api.python.langchain.com/en/latest/document_loaders/langchain_community.document_loaders.xml.UnstructuredXMLLoader.html", "title": "XML"}]-->
from langchain_community.document_loaders import UnstructuredXMLLoader

loader = UnstructuredXMLLoader(
    "./example_data/factbook.xml",
)
docs = loader.load()
docs[0]
```


## Related

- Document loader [conceptual guide](/docs/concepts/#document-loaders)
- Document loader [how-to guides](/docs/how_to/#document-loaders)