---
canonical: https://python.langchain.com/v0.2/docs/versions/migrating_chains/stuff_docs_chain/
custom_edit_url: https://github.com/langchain-ai/langchain/edit/master/docs/docs/versions/migrating_chains/stuff_docs_chain.ipynb
title: Migrating from StuffDocumentsChain
---

[StuffDocumentsChain](https://api.python.langchain.com/en/latest/chains/langchain.chains.combine_documents.stuff.StuffDocumentsChain.html) combines documents by concatenating them into a single context window. It is a straightforward and effective strategy for combining documents for question-answering, summarization, and other purposes.

[create_stuff_documents_chain](https://api.python.langchain.com/en/latest/chains/langchain.chains.combine_documents.stuff.create_stuff_documents_chain.html) is the recommended alternative. It functions the same as `StuffDocumentsChain`, with better support for streaming and batch functionality. Because it is a simple combination of [LCEL primitives](/docs/concepts/#langchain-expression-language-lcel), it is also easier to extend and incorporate into other LangChain applications.

Below we will go through both `StuffDocumentsChain` and `create_stuff_documents_chain` on a simple example for illustrative purposes.

Let's first load a chat model:

import ChatModelTabs from "@theme/ChatModelTabs";

<ChatModelTabs customVarName="llm" />

## Example

Let's go through an example where we analyze a set of documents. We first generate some simple documents for illustrative purposes:


```python
<!--IMPORTS:[{"imported": "Document", "source": "langchain_core.documents", "docs": "https://api.python.langchain.com/en/latest/documents/langchain_core.documents.base.Document.html", "title": "# Example"}]-->
from langchain_core.documents import Document

documents = [
    Document(page_content="Apples are red", metadata={"title": "apple_book"}),
    Document(page_content="Blueberries are blue", metadata={"title": "blueberry_book"}),
    Document(page_content="Bananas are yelow", metadata={"title": "banana_book"}),
]
```

### Legacy

<details open>

Below we show an implementation with `StuffDocumentsChain`. We define the prompt template for a summarization task and instantiate a [LLMChain](https://api.python.langchain.com/en/latest/chains/langchain.chains.llm.LLMChain.html) object for this purpose. We define how documents are formatted into the prompt and ensure consistency among the keys in the various prompts.


```python
<!--IMPORTS:[{"imported": "LLMChain", "source": "langchain.chains", "docs": "https://api.python.langchain.com/en/latest/chains/langchain.chains.llm.LLMChain.html", "title": "# Example"}, {"imported": "StuffDocumentsChain", "source": "langchain.chains", "docs": "https://api.python.langchain.com/en/latest/chains/langchain.chains.combine_documents.stuff.StuffDocumentsChain.html", "title": "# Example"}, {"imported": "ChatPromptTemplate", "source": "langchain_core.prompts", "docs": "https://api.python.langchain.com/en/latest/prompts/langchain_core.prompts.chat.ChatPromptTemplate.html", "title": "# Example"}, {"imported": "PromptTemplate", "source": "langchain_core.prompts", "docs": "https://api.python.langchain.com/en/latest/prompts/langchain_core.prompts.prompt.PromptTemplate.html", "title": "# Example"}]-->
from langchain.chains import LLMChain, StuffDocumentsChain
from langchain_core.prompts import ChatPromptTemplate, PromptTemplate

# This controls how each document will be formatted. Specifically,
# it will be passed to `format_document` - see that function for more
# details.
document_prompt = PromptTemplate(
    input_variables=["page_content"], template="{page_content}"
)
document_variable_name = "context"
# The prompt here should take as an input variable the
# `document_variable_name`
prompt = ChatPromptTemplate.from_template("Summarize this content: {context}")

llm_chain = LLMChain(llm=llm, prompt=prompt)
chain = StuffDocumentsChain(
    llm_chain=llm_chain,
    document_prompt=document_prompt,
    document_variable_name=document_variable_name,
)
```

We can now invoke our chain:


```python
result = chain.invoke(documents)
result["output_text"]
```



```output
'This content describes the colors of different fruits: apples are red, blueberries are blue, and bananas are yellow.'
```



```python
for chunk in chain.stream(documents):
    print(chunk)
```
```output
{'input_documents': [Document(metadata={'title': 'apple_book'}, page_content='Apples are red'), Document(metadata={'title': 'blueberry_book'}, page_content='Blueberries are blue'), Document(metadata={'title': 'banana_book'}, page_content='Bananas are yelow')], 'output_text': 'This content describes the colors of different fruits: apples are red, blueberries are blue, and bananas are yellow.'}
```
</details>

### LCEL

<details open>

Below we show an implementation using `create_stuff_documents_chain`:


```python
<!--IMPORTS:[{"imported": "create_stuff_documents_chain", "source": "langchain.chains.combine_documents", "docs": "https://api.python.langchain.com/en/latest/chains/langchain.chains.combine_documents.stuff.create_stuff_documents_chain.html", "title": "# Example"}, {"imported": "ChatPromptTemplate", "source": "langchain_core.prompts", "docs": "https://api.python.langchain.com/en/latest/prompts/langchain_core.prompts.chat.ChatPromptTemplate.html", "title": "# Example"}]-->
from langchain.chains.combine_documents import create_stuff_documents_chain
from langchain_core.prompts import ChatPromptTemplate

prompt = ChatPromptTemplate.from_template("Summarize this content: {context}")
chain = create_stuff_documents_chain(llm, prompt)
```

Invoking the chain, we obtain a similar result as before:


```python
result = chain.invoke({"context": documents})
result
```



```output
'This content describes the colors of different fruits: apples are red, blueberries are blue, and bananas are yellow.'
```


Note that this implementation supports streaming of output tokens:


```python
for chunk in chain.stream({"context": documents}):
    print(chunk, end=" | ")
```
```output
 | This |  content |  describes |  the |  colors |  of |  different |  fruits | : |  apples |  are |  red | , |  blue | berries |  are |  blue | , |  and |  bananas |  are |  yellow | . |  |
```
</details>

## Next steps

Check out the [LCEL conceptual docs](/docs/concepts/#langchain-expression-language-lcel) for more background information.

See these [how-to guides](/docs/how_to/#qa-with-rag) for more on question-answering tasks with RAG.

See [this tutorial](/docs/tutorials/summarization/) for more LLM-based summarization strategies.