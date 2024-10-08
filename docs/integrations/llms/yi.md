---
canonical: https://python.langchain.com/v0.2/docs/integrations/llms/yi/
custom_edit_url: https://github.com/langchain-ai/langchain/edit/master/docs/docs/integrations/llms/yi.ipynb
---

# Yi
[01.AI](https://www.lingyiwanwu.com/en), founded by Dr. Kai-Fu Lee, is a global company at the forefront of AI 2.0. They offer cutting-edge large language models, including the Yi series, which range from 6B to hundreds of billions of parameters. 01.AI also provides multimodal models, an open API platform, and open-source options like Yi-34B/9B/6B and Yi-VL.


```python
## Installing the langchain packages needed to use the integration
%pip install -qU langchain-community
```

## Prerequisite
An API key is required to access Yi LLM API. Visit https://www.lingyiwanwu.com/ to get your API key. When applying for the API key, you need to specify whether it's for domestic (China) or international use.

## Use Yi LLM


```python
<!--IMPORTS:[{"imported": "YiLLM", "source": "langchain_community.llms", "docs": "https://api.python.langchain.com/en/latest/llms/langchain_community.llms.yi.YiLLM.html", "title": "Yi"}]-->
import os

os.environ["YI_API_KEY"] = "YOUR_API_KEY"

from langchain_community.llms import YiLLM

# Load the model
llm = YiLLM(model="yi-large")

# You can specify the region if needed (default is "auto")
# llm = YiLLM(model="yi-large", region="domestic")  # or "international"

# Basic usage
res = llm.invoke("What's your name?")
print(res)
```


```python
# Generate method
res = llm.generate(
    prompts=[
        "Explain the concept of large language models.",
        "What are the potential applications of AI in healthcare?",
    ]
)
print(res)
```


```python
# Streaming
for chunk in llm.stream("Describe the key features of the Yi language model series."):
    print(chunk, end="", flush=True)
```


```python
# Asynchronous streaming
import asyncio


async def run_aio_stream():
    async for chunk in llm.astream(
        "Write a brief on the future of AI according to Dr. Kai-Fu Lee's vision."
    ):
        print(chunk, end="", flush=True)


asyncio.run(run_aio_stream())
```


```python
# Adjusting parameters
llm_with_params = YiLLM(
    model="yi-large",
    temperature=0.7,
    top_p=0.9,
)

res = llm_with_params(
    "Propose an innovative AI application that could benefit society."
)
print(res)
```


## Related

- LLM [conceptual guide](/docs/concepts/#llms)
- LLM [how-to guides](/docs/how_to/#llms)