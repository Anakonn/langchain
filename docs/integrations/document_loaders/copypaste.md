---
canonical: https://python.langchain.com/v0.2/docs/integrations/document_loaders/copypaste/
custom_edit_url: https://github.com/langchain-ai/langchain/edit/master/docs/docs/integrations/document_loaders/copypaste.ipynb
---

# Copy Paste

This notebook covers how to load a document object from something you just want to copy and paste. In this case, you don't even need to use a DocumentLoader, but rather can just construct the Document directly.


```python
<!--IMPORTS:[{"imported": "Document", "source": "langchain_core.documents", "docs": "https://api.python.langchain.com/en/latest/documents/langchain_core.documents.base.Document.html", "title": "Copy Paste"}]-->
from langchain_core.documents import Document
```


```python
text = "..... put the text you copy pasted here......"
```


```python
doc = Document(page_content=text)
```

## Metadata
If you want to add metadata about the where you got this piece of text, you easily can with the metadata key.


```python
metadata = {"source": "internet", "date": "Friday"}
```


```python
doc = Document(page_content=text, metadata=metadata)
```


## Related

- Document loader [conceptual guide](/docs/concepts/#document-loaders)
- Document loader [how-to guides](/docs/how_to/#document-loaders)