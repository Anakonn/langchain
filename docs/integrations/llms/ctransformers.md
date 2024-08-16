---
canonical: https://python.langchain.com/v0.2/docs/integrations/llms/ctransformers/
custom_edit_url: https://github.com/langchain-ai/langchain/edit/master/docs/docs/integrations/llms/ctransformers.ipynb
---

# C Transformers

The [C Transformers](https://github.com/marella/ctransformers) library provides Python bindings for GGML models.

This example goes over how to use LangChain to interact with `C Transformers` [models](https://github.com/marella/ctransformers#supported-models).

**Install**


```python
%pip install --upgrade --quiet  ctransformers
```

**Load Model**


```python
<!--IMPORTS:[{"imported": "CTransformers", "source": "langchain_community.llms", "docs": "https://api.python.langchain.com/en/latest/llms/langchain_community.llms.ctransformers.CTransformers.html", "title": "C Transformers"}]-->
from langchain_community.llms import CTransformers

llm = CTransformers(model="marella/gpt-2-ggml")
```

**Generate Text**


```python
print(llm.invoke("AI is going to"))
```

**Streaming**


```python
<!--IMPORTS:[{"imported": "StreamingStdOutCallbackHandler", "source": "langchain_core.callbacks", "docs": "https://api.python.langchain.com/en/latest/callbacks/langchain_core.callbacks.streaming_stdout.StreamingStdOutCallbackHandler.html", "title": "C Transformers"}]-->
from langchain_core.callbacks import StreamingStdOutCallbackHandler

llm = CTransformers(
    model="marella/gpt-2-ggml", callbacks=[StreamingStdOutCallbackHandler()]
)

response = llm.invoke("AI is going to")
```

**LLMChain**


```python
<!--IMPORTS:[{"imported": "LLMChain", "source": "langchain.chains", "docs": "https://api.python.langchain.com/en/latest/chains/langchain.chains.llm.LLMChain.html", "title": "C Transformers"}, {"imported": "PromptTemplate", "source": "langchain_core.prompts", "docs": "https://api.python.langchain.com/en/latest/prompts/langchain_core.prompts.prompt.PromptTemplate.html", "title": "C Transformers"}]-->
from langchain.chains import LLMChain
from langchain_core.prompts import PromptTemplate

template = """Question: {question}

Answer:"""

prompt = PromptTemplate.from_template(template)

llm_chain = LLMChain(prompt=prompt, llm=llm)

response = llm_chain.run("What is AI?")
```


## Related

- LLM [conceptual guide](/docs/concepts/#llms)
- LLM [how-to guides](/docs/how_to/#llms)