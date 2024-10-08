---
canonical: https://python.langchain.com/v0.2/docs/integrations/text_embedding/clarifai/
custom_edit_url: https://github.com/langchain-ai/langchain/edit/master/docs/docs/integrations/text_embedding/clarifai.ipynb
---

# Clarifai

>[Clarifai](https://www.clarifai.com/) is an AI Platform that provides the full AI lifecycle ranging from data exploration, data labeling, model training, evaluation, and inference.

This example goes over how to use LangChain to interact with `Clarifai` [models](https://clarifai.com/explore/models). Text embedding models in particular can be found [here](https://clarifai.com/explore/models?page=1&perPage=24&filterData=%5B%7B%22field%22%3A%22model_type_id%22%2C%22value%22%3A%5B%22text-embedder%22%5D%7D%5D).

To use Clarifai, you must have an account and a Personal Access Token (PAT) key. 
[Check here](https://clarifai.com/settings/security) to get or create a PAT.

# Dependencies


```python
# Install required dependencies
%pip install --upgrade --quiet  clarifai
```

# Imports
Here we will be setting the personal access token. You can find your PAT under [settings/security](https://clarifai.com/settings/security) in your Clarifai account.


```python
# Please login and get your API key from  https://clarifai.com/settings/security
from getpass import getpass

CLARIFAI_PAT = getpass()
```


```python
<!--IMPORTS:[{"imported": "LLMChain", "source": "langchain.chains", "docs": "https://api.python.langchain.com/en/latest/chains/langchain.chains.llm.LLMChain.html", "title": "Clarifai"}, {"imported": "ClarifaiEmbeddings", "source": "langchain_community.embeddings", "docs": "https://api.python.langchain.com/en/latest/embeddings/langchain_community.embeddings.clarifai.ClarifaiEmbeddings.html", "title": "Clarifai"}, {"imported": "PromptTemplate", "source": "langchain_core.prompts", "docs": "https://api.python.langchain.com/en/latest/prompts/langchain_core.prompts.prompt.PromptTemplate.html", "title": "Clarifai"}]-->
# Import the required modules
from langchain.chains import LLMChain
from langchain_community.embeddings import ClarifaiEmbeddings
from langchain_core.prompts import PromptTemplate
```

# Input
Create a prompt template to be used with the LLM Chain:


```python
template = """Question: {question}

Answer: Let's think step by step."""

prompt = PromptTemplate.from_template(template)
```

# Setup
Set the user id and app id to the application in which the model resides. You can find a list of public models on https://clarifai.com/explore/models

You will have to also initialize the model id and if needed, the model version id. Some models have many versions, you can choose the one appropriate for your task.


```python
USER_ID = "clarifai"
APP_ID = "main"
MODEL_ID = "BAAI-bge-base-en-v15"
MODEL_URL = "https://clarifai.com/clarifai/main/models/BAAI-bge-base-en-v15"

# Further you can also provide a specific model version as the model_version_id arg.
# MODEL_VERSION_ID = "MODEL_VERSION_ID"
```


```python
# Initialize a Clarifai embedding model
embeddings = ClarifaiEmbeddings(user_id=USER_ID, app_id=APP_ID, model_id=MODEL_ID)

# Initialize a clarifai embedding model using model URL
embeddings = ClarifaiEmbeddings(model_url=MODEL_URL)

# Alternatively you can initialize clarifai class with pat argument.
```


```python
text = "roses are red violets are blue."
text2 = "Make hay while the sun shines."
```

You can embed single line of your text using embed_query function !


```python
query_result = embeddings.embed_query(text)
```

Further to embed list of texts/documents use embed_documents function.


```python
doc_result = embeddings.embed_documents([text, text2])
```


## Related

- Embedding model [conceptual guide](/docs/concepts/#embedding-models)
- Embedding model [how-to guides](/docs/how_to/#embedding-models)