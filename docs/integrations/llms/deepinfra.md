---
canonical: https://python.langchain.com/v0.2/docs/integrations/llms/deepinfra/
custom_edit_url: https://github.com/langchain-ai/langchain/edit/master/docs/docs/integrations/llms/deepinfra.ipynb
---

# DeepInfra

[DeepInfra](https://deepinfra.com/?utm_source=langchain) is a serverless inference as a service that provides access to a [variety of LLMs](https://deepinfra.com/models?utm_source=langchain) and [embeddings models](https://deepinfra.com/models?type=embeddings&utm_source=langchain). This notebook goes over how to use LangChain with DeepInfra for language models.

## Set the Environment API Key
Make sure to get your API key from DeepInfra. You have to [Login](https://deepinfra.com/login?from=%2Fdash) and get a new token.

You are given a 1 hour free of serverless GPU compute to test different models. (see [here](https://github.com/deepinfra/deepctl#deepctl))
You can print your token with `deepctl auth token`


```python
# get a new token: https://deepinfra.com/login?from=%2Fdash

from getpass import getpass

DEEPINFRA_API_TOKEN = getpass()
```
```output
 ········
```

```python
import os

os.environ["DEEPINFRA_API_TOKEN"] = DEEPINFRA_API_TOKEN
```

## Create the DeepInfra instance
You can also use our open-source [deepctl tool](https://github.com/deepinfra/deepctl#deepctl) to manage your model deployments. You can view a list of available parameters [here](https://deepinfra.com/databricks/dolly-v2-12b#API).


```python
<!--IMPORTS:[{"imported": "DeepInfra", "source": "langchain_community.llms", "docs": "https://api.python.langchain.com/en/latest/llms/langchain_community.llms.deepinfra.DeepInfra.html", "title": "DeepInfra"}]-->
from langchain_community.llms import DeepInfra

llm = DeepInfra(model_id="meta-llama/Llama-2-70b-chat-hf")
llm.model_kwargs = {
    "temperature": 0.7,
    "repetition_penalty": 1.2,
    "max_new_tokens": 250,
    "top_p": 0.9,
}
```


```python
# run inferences directly via wrapper
llm("Who let the dogs out?")
```



```output
'This is a question that has puzzled many people'
```



```python
# run streaming inference
for chunk in llm.stream("Who let the dogs out?"):
    print(chunk)
```



```output
 Will
 Smith
.
```


## Create a Prompt Template
We will create a prompt template for Question and Answer.


```python
<!--IMPORTS:[{"imported": "PromptTemplate", "source": "langchain_core.prompts", "docs": "https://api.python.langchain.com/en/latest/prompts/langchain_core.prompts.prompt.PromptTemplate.html", "title": "DeepInfra"}]-->
from langchain_core.prompts import PromptTemplate

template = """Question: {question}

Answer: Let's think step by step."""

prompt = PromptTemplate.from_template(template)
```

## Initiate the LLMChain


```python
<!--IMPORTS:[{"imported": "LLMChain", "source": "langchain.chains", "docs": "https://api.python.langchain.com/en/latest/chains/langchain.chains.llm.LLMChain.html", "title": "DeepInfra"}]-->
from langchain.chains import LLMChain

llm_chain = LLMChain(prompt=prompt, llm=llm)
```

## Run the LLMChain
Provide a question and run the LLMChain.


```python
question = "Can penguins reach the North pole?"

llm_chain.run(question)
```



```output
"Penguins are found in Antarctica and the surrounding islands, which are located at the southernmost tip of the planet. The North Pole is located at the northernmost tip of the planet, and it would be a long journey for penguins to get there. In fact, penguins don't have the ability to fly or migrate over such long distances. So, no, penguins cannot reach the North Pole. "
```



## Related

- LLM [conceptual guide](/docs/concepts/#llms)
- LLM [how-to guides](/docs/how_to/#llms)