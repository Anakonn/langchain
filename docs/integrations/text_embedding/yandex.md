---
canonical: https://python.langchain.com/v0.2/docs/integrations/text_embedding/yandex/
custom_edit_url: https://github.com/langchain-ai/langchain/edit/master/docs/docs/integrations/text_embedding/yandex.ipynb
---

# YandexGPT

This notebook goes over how to use Langchain with [YandexGPT](https://cloud.yandex.com/en/services/yandexgpt) embeddings models.

To use, you should have the `yandexcloud` python package installed.


```python
%pip install --upgrade --quiet  yandexcloud
```

First, you should [create service account](https://cloud.yandex.com/en/docs/iam/operations/sa/create) with the `ai.languageModels.user` role.

Next, you have two authentication options:
- [IAM token](https://cloud.yandex.com/en/docs/iam/operations/iam-token/create-for-sa).
    You can specify the token in a constructor parameter `iam_token` or in an environment variable `YC_IAM_TOKEN`.
- [API key](https://cloud.yandex.com/en/docs/iam/operations/api-key/create)
    You can specify the key in a constructor parameter `api_key` or in an environment variable `YC_API_KEY`.

To specify the model you can use `model_uri` parameter, see [the documentation](https://cloud.yandex.com/en/docs/yandexgpt/concepts/models#yandexgpt-embeddings) for more details.

By default, the latest version of `text-search-query` is used from the folder specified in the parameter `folder_id` or `YC_FOLDER_ID` environment variable.


```python
<!--IMPORTS:[{"imported": "YandexGPTEmbeddings", "source": "langchain_community.embeddings.yandex", "docs": "https://api.python.langchain.com/en/latest/embeddings/langchain_community.embeddings.yandex.YandexGPTEmbeddings.html", "title": "YandexGPT"}]-->
from langchain_community.embeddings.yandex import YandexGPTEmbeddings
```


```python
embeddings = YandexGPTEmbeddings()
```


```python
text = "This is a test document."
```


```python
query_result = embeddings.embed_query(text)
```


```python
doc_result = embeddings.embed_documents([text])
```


```python
query_result[:5]
```



```output
[-0.021392822265625,
 0.096435546875,
 -0.046966552734375,
 -0.0183258056640625,
 -0.00555419921875]
```



```python
doc_result[0][:5]
```



```output
[-0.021392822265625,
 0.096435546875,
 -0.046966552734375,
 -0.0183258056640625,
 -0.00555419921875]
```



## Related

- Embedding model [conceptual guide](/docs/concepts/#embedding-models)
- Embedding model [how-to guides](/docs/how_to/#embedding-models)