---
canonical: https://python.langchain.com/v0.2/docs/integrations/document_loaders/stripe/
custom_edit_url: https://github.com/langchain-ai/langchain/edit/master/docs/docs/integrations/document_loaders/stripe.ipynb
---

# Stripe

>[Stripe](https://stripe.com/en-ca) is an Irish-American financial services and software as a service (SaaS) company. It offers payment-processing software and application programming interfaces for e-commerce websites and mobile applications.

This notebook covers how to load data from the `Stripe REST API` into a format that can be ingested into LangChain, along with example usage for vectorization.


```python
<!--IMPORTS:[{"imported": "VectorstoreIndexCreator", "source": "langchain.indexes", "docs": "https://api.python.langchain.com/en/latest/indexes/langchain.indexes.vectorstore.VectorstoreIndexCreator.html", "title": "Stripe"}, {"imported": "StripeLoader", "source": "langchain_community.document_loaders", "docs": "https://api.python.langchain.com/en/latest/document_loaders/langchain_community.document_loaders.stripe.StripeLoader.html", "title": "Stripe"}]-->
from langchain.indexes import VectorstoreIndexCreator
from langchain_community.document_loaders import StripeLoader
```

The Stripe API requires an access token, which can be found inside of the Stripe dashboard.

This document loader also requires a `resource` option which defines what data you want to load.

Following resources are available:

`balance_transations` [Documentation](https://stripe.com/docs/api/balance_transactions/list)

`charges` [Documentation](https://stripe.com/docs/api/charges/list)

`customers` [Documentation](https://stripe.com/docs/api/customers/list)

`events` [Documentation](https://stripe.com/docs/api/events/list)

`refunds` [Documentation](https://stripe.com/docs/api/refunds/list)

`disputes` [Documentation](https://stripe.com/docs/api/disputes/list)


```python
stripe_loader = StripeLoader("charges")
```


```python
# Create a vectorstore retriever from the loader
# see https://python.langchain.com/en/latest/modules/data_connection/getting_started.html for more details

index = VectorstoreIndexCreator().from_loaders([stripe_loader])
stripe_doc_retriever = index.vectorstore.as_retriever()
```


## Related

- Document loader [conceptual guide](/docs/concepts/#document-loaders)
- Document loader [how-to guides](/docs/how_to/#document-loaders)