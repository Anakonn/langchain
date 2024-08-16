---
canonical: https://python.langchain.com/v0.2/docs/integrations/document_loaders/kinetica/
custom_edit_url: https://github.com/langchain-ai/langchain/edit/master/docs/docs/integrations/document_loaders/kinetica.ipynb
---

# Kinetica

This notebooks goes over how to load documents from Kinetica


```python
%pip install gpudb==7.2.0.9
```


```python
<!--IMPORTS:[{"imported": "KineticaLoader", "source": "langchain_community.document_loaders.kinetica_loader", "docs": "https://api.python.langchain.com/en/latest/document_loaders/langchain_community.document_loaders.kinetica_loader.KineticaLoader.html", "title": "Kinetica"}]-->
from langchain_community.document_loaders.kinetica_loader import KineticaLoader
```


```python
<!--IMPORTS:[{"imported": "KineticaSettings", "source": "langchain_community.vectorstores", "docs": "https://api.python.langchain.com/en/latest/vectorstores/langchain_community.vectorstores.kinetica.KineticaSettings.html", "title": "Kinetica"}]-->
## Loading Environment Variables
import os

from dotenv import load_dotenv
from langchain_community.vectorstores import (
    KineticaSettings,
)

load_dotenv()
```


```python
# Kinetica needs the connection to the database.
# This is how to set it up.
HOST = os.getenv("KINETICA_HOST", "http://127.0.0.1:9191")
USERNAME = os.getenv("KINETICA_USERNAME", "")
PASSWORD = os.getenv("KINETICA_PASSWORD", "")


def create_config() -> KineticaSettings:
    return KineticaSettings(host=HOST, username=USERNAME, password=PASSWORD)
```


```python
<!--IMPORTS:[{"imported": "KineticaLoader", "source": "langchain_community.document_loaders.kinetica_loader", "docs": "https://api.python.langchain.com/en/latest/document_loaders/langchain_community.document_loaders.kinetica_loader.KineticaLoader.html", "title": "Kinetica"}]-->
from langchain_community.document_loaders.kinetica_loader import KineticaLoader

# The following `QUERY` is an example which will not run; this
# needs to be substituted with a valid `QUERY` that will return
# data and the `SCHEMA.TABLE` combination must exist in Kinetica.

QUERY = "select text, survey_id from SCHEMA.TABLE limit 10"
kinetica_loader = KineticaLoader(
    QUERY,
    HOST,
    USERNAME,
    PASSWORD,
)
kinetica_documents = kinetica_loader.load()
print(kinetica_documents)
```


```python
<!--IMPORTS:[{"imported": "KineticaLoader", "source": "langchain_community.document_loaders.kinetica_loader", "docs": "https://api.python.langchain.com/en/latest/document_loaders/langchain_community.document_loaders.kinetica_loader.KineticaLoader.html", "title": "Kinetica"}]-->
from langchain_community.document_loaders.kinetica_loader import KineticaLoader

# The following `QUERY` is an example which will not run; this
# needs to be substituted with a valid `QUERY` that will return
# data and the `SCHEMA.TABLE` combination must exist in Kinetica.

QUERY = "select text, survey_id as source from SCHEMA.TABLE limit 10"
kl = KineticaLoader(
    query=QUERY,
    host=HOST,
    username=USERNAME,
    password=PASSWORD,
    metadata_columns=["source"],
)
kinetica_documents = kl.load()
print(kinetica_documents)
```


## Related

- Document loader [conceptual guide](/docs/concepts/#document-loaders)
- Document loader [how-to guides](/docs/how_to/#document-loaders)