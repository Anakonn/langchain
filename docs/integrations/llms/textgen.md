---
canonical: https://python.langchain.com/v0.2/docs/integrations/llms/textgen/
custom_edit_url: https://github.com/langchain-ai/langchain/edit/master/docs/docs/integrations/llms/textgen.ipynb
---

# TextGen

[GitHub:oobabooga/text-generation-webui](https://github.com/oobabooga/text-generation-webui) A gradio web UI for running Large Language Models like LLaMA, llama.cpp, GPT-J, Pythia, OPT, and GALACTICA.

This example goes over how to use LangChain to interact with LLM models via the `text-generation-webui` API integration.

Please ensure that you have `text-generation-webui` configured and an LLM installed.  Recommended installation via the [one-click installer appropriate](https://github.com/oobabooga/text-generation-webui#one-click-installers) for your OS.

Once `text-generation-webui` is installed and confirmed working via the web interface, please enable the `api` option either through the web model configuration tab, or by adding the run-time arg `--api` to your start command.

## Set model_url and run the example


```python
model_url = "http://localhost:5000"
```


```python
<!--IMPORTS:[{"imported": "LLMChain", "source": "langchain.chains", "docs": "https://api.python.langchain.com/en/latest/chains/langchain.chains.llm.LLMChain.html", "title": "TextGen"}, {"imported": "set_debug", "source": "langchain.globals", "docs": "https://api.python.langchain.com/en/latest/globals/langchain.globals.set_debug.html", "title": "TextGen"}, {"imported": "TextGen", "source": "langchain_community.llms", "docs": "https://api.python.langchain.com/en/latest/llms/langchain_community.llms.textgen.TextGen.html", "title": "TextGen"}, {"imported": "PromptTemplate", "source": "langchain_core.prompts", "docs": "https://api.python.langchain.com/en/latest/prompts/langchain_core.prompts.prompt.PromptTemplate.html", "title": "TextGen"}]-->
from langchain.chains import LLMChain
from langchain.globals import set_debug
from langchain_community.llms import TextGen
from langchain_core.prompts import PromptTemplate

set_debug(True)

template = """Question: {question}

Answer: Let's think step by step."""


prompt = PromptTemplate.from_template(template)
llm = TextGen(model_url=model_url)
llm_chain = LLMChain(prompt=prompt, llm=llm)
question = "What NFL team won the Super Bowl in the year Justin Bieber was born?"

llm_chain.run(question)
```

### Streaming Version

You should install websocket-client to use this feature.
`pip install websocket-client`


```python
model_url = "ws://localhost:5005"
```


```python
<!--IMPORTS:[{"imported": "LLMChain", "source": "langchain.chains", "docs": "https://api.python.langchain.com/en/latest/chains/langchain.chains.llm.LLMChain.html", "title": "TextGen"}, {"imported": "set_debug", "source": "langchain.globals", "docs": "https://api.python.langchain.com/en/latest/globals/langchain.globals.set_debug.html", "title": "TextGen"}, {"imported": "TextGen", "source": "langchain_community.llms", "docs": "https://api.python.langchain.com/en/latest/llms/langchain_community.llms.textgen.TextGen.html", "title": "TextGen"}, {"imported": "StreamingStdOutCallbackHandler", "source": "langchain_core.callbacks", "docs": "https://api.python.langchain.com/en/latest/callbacks/langchain_core.callbacks.streaming_stdout.StreamingStdOutCallbackHandler.html", "title": "TextGen"}, {"imported": "PromptTemplate", "source": "langchain_core.prompts", "docs": "https://api.python.langchain.com/en/latest/prompts/langchain_core.prompts.prompt.PromptTemplate.html", "title": "TextGen"}]-->
from langchain.chains import LLMChain
from langchain.globals import set_debug
from langchain_community.llms import TextGen
from langchain_core.callbacks import StreamingStdOutCallbackHandler
from langchain_core.prompts import PromptTemplate

set_debug(True)

template = """Question: {question}

Answer: Let's think step by step."""


prompt = PromptTemplate.from_template(template)
llm = TextGen(
    model_url=model_url, streaming=True, callbacks=[StreamingStdOutCallbackHandler()]
)
llm_chain = LLMChain(prompt=prompt, llm=llm)
question = "What NFL team won the Super Bowl in the year Justin Bieber was born?"

llm_chain.run(question)
```


```python
llm = TextGen(model_url=model_url, streaming=True)
for chunk in llm.stream("Ask 'Hi, how are you?' like a pirate:'", stop=["'", "\n"]):
    print(chunk, end="", flush=True)
```


## Related

- LLM [conceptual guide](/docs/concepts/#llms)
- LLM [how-to guides](/docs/how_to/#llms)