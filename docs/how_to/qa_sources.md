---
canonical: https://python.langchain.com/v0.2/docs/how_to/qa_sources/
custom_edit_url: https://github.com/langchain-ai/langchain/edit/master/docs/docs/how_to/qa_sources.ipynb
---

# How to get your RAG application to return sources

Often in Q&A applications it's important to show users the sources that were used to generate the answer. The simplest way to do this is for the chain to return the Documents that were retrieved in each generation.

We'll work off of the Q&A app we built over the [LLM Powered Autonomous Agents](https://lilianweng.github.io/posts/2023-06-23-agent/) blog post by Lilian Weng in the [RAG tutorial](/docs/tutorials/rag).

We will cover two approaches:

1. Using the built-in [create_retrieval_chain](https://api.python.langchain.com/en/latest/chains/langchain.chains.retrieval.create_retrieval_chain.html), which returns sources by default;
2. Using a simple [LCEL](/docs/concepts#langchain-expression-language-lcel) implementation, to show the operating principle.

We will also show how to structure sources into the model response, such that a model can report what specific sources it used in generating its answer.

## Setup

### Dependencies

We'll use OpenAI embeddings and a Chroma vector store in this walkthrough, but everything shown here works with any [Embeddings](/docs/concepts#embedding-models), [VectorStore](/docs/concepts#vectorstores) or [Retriever](/docs/concepts#retrievers). 

We'll use the following packages:


```python
%pip install --upgrade --quiet  langchain langchain-community langchainhub langchain-openai langchain-chroma bs4
```

We need to set environment variable `OPENAI_API_KEY`, which can be done directly or loaded from a `.env` file like so:


```python
import getpass
import os

os.environ["OPENAI_API_KEY"] = getpass.getpass()

# import dotenv

# dotenv.load_dotenv()
```

### LangSmith

Many of the applications you build with LangChain will contain multiple steps with multiple invocations of LLM calls. As these applications get more and more complex, it becomes crucial to be able to inspect what exactly is going on inside your chain or agent. The best way to do this is with [LangSmith](https://smith.langchain.com).

Note that LangSmith is not needed, but it is helpful. If you do want to use LangSmith, after you sign up at the link above, make sure to set your environment variables to start logging traces:


```python
os.environ["LANGCHAIN_TRACING_V2"] = "true"
os.environ["LANGCHAIN_API_KEY"] = getpass.getpass()
```

## Using `create_retrieval_chain`

Let's first select a LLM:

import ChatModelTabs from "@theme/ChatModelTabs";

<ChatModelTabs customVarName="llm" />

Here is Q&A app with sources we built over the [LLM Powered Autonomous Agents](https://lilianweng.github.io/posts/2023-06-23-agent/) blog post by Lilian Weng in the [RAG tutorial](/docs/tutorials/rag):


```python
<!--IMPORTS:[{"imported": "create_retrieval_chain", "source": "langchain.chains", "docs": "https://api.python.langchain.com/en/latest/chains/langchain.chains.retrieval.create_retrieval_chain.html", "title": "How to get your RAG application to return sources"}, {"imported": "create_stuff_documents_chain", "source": "langchain.chains.combine_documents", "docs": "https://api.python.langchain.com/en/latest/chains/langchain.chains.combine_documents.stuff.create_stuff_documents_chain.html", "title": "How to get your RAG application to return sources"}, {"imported": "Chroma", "source": "langchain_chroma", "docs": "https://api.python.langchain.com/en/latest/vectorstores/langchain_chroma.vectorstores.Chroma.html", "title": "How to get your RAG application to return sources"}, {"imported": "WebBaseLoader", "source": "langchain_community.document_loaders", "docs": "https://api.python.langchain.com/en/latest/document_loaders/langchain_community.document_loaders.web_base.WebBaseLoader.html", "title": "How to get your RAG application to return sources"}, {"imported": "ChatPromptTemplate", "source": "langchain_core.prompts", "docs": "https://api.python.langchain.com/en/latest/prompts/langchain_core.prompts.chat.ChatPromptTemplate.html", "title": "How to get your RAG application to return sources"}, {"imported": "OpenAIEmbeddings", "source": "langchain_openai", "docs": "https://api.python.langchain.com/en/latest/embeddings/langchain_openai.embeddings.base.OpenAIEmbeddings.html", "title": "How to get your RAG application to return sources"}, {"imported": "RecursiveCharacterTextSplitter", "source": "langchain_text_splitters", "docs": "https://api.python.langchain.com/en/latest/character/langchain_text_splitters.character.RecursiveCharacterTextSplitter.html", "title": "How to get your RAG application to return sources"}]-->
import bs4
from langchain.chains import create_retrieval_chain
from langchain.chains.combine_documents import create_stuff_documents_chain
from langchain_chroma import Chroma
from langchain_community.document_loaders import WebBaseLoader
from langchain_core.prompts import ChatPromptTemplate
from langchain_openai import OpenAIEmbeddings
from langchain_text_splitters import RecursiveCharacterTextSplitter

# 1. Load, chunk and index the contents of the blog to create a retriever.
loader = WebBaseLoader(
    web_paths=("https://lilianweng.github.io/posts/2023-06-23-agent/",),
    bs_kwargs=dict(
        parse_only=bs4.SoupStrainer(
            class_=("post-content", "post-title", "post-header")
        )
    ),
)
docs = loader.load()

text_splitter = RecursiveCharacterTextSplitter(chunk_size=1000, chunk_overlap=200)
splits = text_splitter.split_documents(docs)
vectorstore = Chroma.from_documents(documents=splits, embedding=OpenAIEmbeddings())
retriever = vectorstore.as_retriever()


# 2. Incorporate the retriever into a question-answering chain.
system_prompt = (
    "You are an assistant for question-answering tasks. "
    "Use the following pieces of retrieved context to answer "
    "the question. If you don't know the answer, say that you "
    "don't know. Use three sentences maximum and keep the "
    "answer concise."
    "\n\n"
    "{context}"
)

prompt = ChatPromptTemplate.from_messages(
    [
        ("system", system_prompt),
        ("human", "{input}"),
    ]
)

question_answer_chain = create_stuff_documents_chain(llm, prompt)
rag_chain = create_retrieval_chain(retriever, question_answer_chain)
```


```python
result = rag_chain.invoke({"input": "What is Task Decomposition?"})
```

Note that `result` is a dict with keys `"input"`, `"context"`, and `"answer"`:


```python
result
```



```output
{'input': 'What is Task Decomposition?',
 'context': [Document(metadata={'source': 'https://lilianweng.github.io/posts/2023-06-23-agent/'}, page_content='Fig. 1. Overview of a LLM-powered autonomous agent system.\nComponent One: Planning#\nA complicated task usually involves many steps. An agent needs to know what they are and plan ahead.\nTask Decomposition#\nChain of thought (CoT; Wei et al. 2022) has become a standard prompting technique for enhancing model performance on complex tasks. The model is instructed to “think step by step” to utilize more test-time computation to decompose hard tasks into smaller and simpler steps. CoT transforms big tasks into multiple manageable tasks and shed lights into an interpretation of the model’s thinking process.'),
  Document(metadata={'source': 'https://lilianweng.github.io/posts/2023-06-23-agent/'}, page_content='Tree of Thoughts (Yao et al. 2023) extends CoT by exploring multiple reasoning possibilities at each step. It first decomposes the problem into multiple thought steps and generates multiple thoughts per step, creating a tree structure. The search process can be BFS (breadth-first search) or DFS (depth-first search) with each state evaluated by a classifier (via a prompt) or majority vote.\nTask decomposition can be done (1) by LLM with simple prompting like "Steps for XYZ.\\n1.", "What are the subgoals for achieving XYZ?", (2) by using task-specific instructions; e.g. "Write a story outline." for writing a novel, or (3) with human inputs.'),
  Document(metadata={'source': 'https://lilianweng.github.io/posts/2023-06-23-agent/'}, page_content='Resources:\n1. Internet access for searches and information gathering.\n2. Long Term memory management.\n3. GPT-3.5 powered Agents for delegation of simple tasks.\n4. File output.\n\nPerformance Evaluation:\n1. Continuously review and analyze your actions to ensure you are performing to the best of your abilities.\n2. Constructively self-criticize your big-picture behavior constantly.\n3. Reflect on past decisions and strategies to refine your approach.\n4. Every command has a cost, so be smart and efficient. Aim to complete tasks in the least number of steps.'),
  Document(metadata={'source': 'https://lilianweng.github.io/posts/2023-06-23-agent/'}, page_content="(3) Task execution: Expert models execute on the specific tasks and log results.\nInstruction:\n\nWith the input and the inference results, the AI assistant needs to describe the process and results. The previous stages can be formed as - User Input: {{ User Input }}, Task Planning: {{ Tasks }}, Model Selection: {{ Model Assignment }}, Task Execution: {{ Predictions }}. You must first answer the user's request in a straightforward manner. Then describe the task process and show your analysis and model inference results to the user in the first person. If inference results contain a file path, must tell the user the complete file path.")],
 'answer': 'Task decomposition involves breaking down a complex task into smaller and more manageable steps. This process helps agents or models tackle difficult tasks by dividing them into simpler subtasks or components. Task decomposition can be achieved through techniques like Chain of Thought or Tree of Thoughts, which guide the agent in breaking down tasks into sequential or branching steps.'}
```


Here, `"context"` contains the sources that the LLM used in generating the response in `"answer"`.

## Custom LCEL implementation

Below we construct a chain similar to those built by `create_retrieval_chain`. It works by building up a dict: 

1. Starting with a dict with the input query, add the retrieved docs in the `"context"` key;
2. Feed both the query and context into a RAG chain and add the result to the dict.


```python
<!--IMPORTS:[{"imported": "StrOutputParser", "source": "langchain_core.output_parsers", "docs": "https://api.python.langchain.com/en/latest/output_parsers/langchain_core.output_parsers.string.StrOutputParser.html", "title": "How to get your RAG application to return sources"}, {"imported": "RunnablePassthrough", "source": "langchain_core.runnables", "docs": "https://api.python.langchain.com/en/latest/runnables/langchain_core.runnables.passthrough.RunnablePassthrough.html", "title": "How to get your RAG application to return sources"}]-->
from langchain_core.output_parsers import StrOutputParser
from langchain_core.runnables import RunnablePassthrough


def format_docs(docs):
    return "\n\n".join(doc.page_content for doc in docs)


# This Runnable takes a dict with keys 'input' and 'context',
# formats them into a prompt, and generates a response.
rag_chain_from_docs = (
    {
        "input": lambda x: x["input"],  # input query
        "context": lambda x: format_docs(x["context"]),  # context
    }
    | prompt  # format query and context into prompt
    | llm  # generate response
    | StrOutputParser()  # coerce to string
)

# Pass input query to retriever
retrieve_docs = (lambda x: x["input"]) | retriever

# Below, we chain `.assign` calls. This takes a dict and successively
# adds keys-- "context" and "answer"-- where the value for each key
# is determined by a Runnable. The Runnable operates on all existing
# keys in the dict.
chain = RunnablePassthrough.assign(context=retrieve_docs).assign(
    answer=rag_chain_from_docs
)

chain.invoke({"input": "What is Task Decomposition"})
```



```output
{'input': 'What is Task Decomposition',
 'context': [Document(metadata={'source': 'https://lilianweng.github.io/posts/2023-06-23-agent/'}, page_content='Fig. 1. Overview of a LLM-powered autonomous agent system.\nComponent One: Planning#\nA complicated task usually involves many steps. An agent needs to know what they are and plan ahead.\nTask Decomposition#\nChain of thought (CoT; Wei et al. 2022) has become a standard prompting technique for enhancing model performance on complex tasks. The model is instructed to “think step by step” to utilize more test-time computation to decompose hard tasks into smaller and simpler steps. CoT transforms big tasks into multiple manageable tasks and shed lights into an interpretation of the model’s thinking process.'),
  Document(metadata={'source': 'https://lilianweng.github.io/posts/2023-06-23-agent/'}, page_content='Tree of Thoughts (Yao et al. 2023) extends CoT by exploring multiple reasoning possibilities at each step. It first decomposes the problem into multiple thought steps and generates multiple thoughts per step, creating a tree structure. The search process can be BFS (breadth-first search) or DFS (depth-first search) with each state evaluated by a classifier (via a prompt) or majority vote.\nTask decomposition can be done (1) by LLM with simple prompting like "Steps for XYZ.\\n1.", "What are the subgoals for achieving XYZ?", (2) by using task-specific instructions; e.g. "Write a story outline." for writing a novel, or (3) with human inputs.'),
  Document(metadata={'source': 'https://lilianweng.github.io/posts/2023-06-23-agent/'}, page_content='The AI assistant can parse user input to several tasks: [{"task": task, "id", task_id, "dep": dependency_task_ids, "args": {"text": text, "image": URL, "audio": URL, "video": URL}}]. The "dep" field denotes the id of the previous task which generates a new resource that the current task relies on. A special tag "-task_id" refers to the generated text image, audio and video in the dependency task with id as task_id. The task MUST be selected from the following options: {{ Available Task List }}. There is a logical relationship between tasks, please note their order. If the user input can\'t be parsed, you need to reply empty JSON. Here are several cases for your reference: {{ Demonstrations }}. The chat history is recorded as {{ Chat History }}. From this chat history, you can find the path of the user-mentioned resources for your task planning.'),
  Document(metadata={'source': 'https://lilianweng.github.io/posts/2023-06-23-agent/'}, page_content='Fig. 11. Illustration of how HuggingGPT works. (Image source: Shen et al. 2023)\nThe system comprises of 4 stages:\n(1) Task planning: LLM works as the brain and parses the user requests into multiple tasks. There are four attributes associated with each task: task type, ID, dependencies, and arguments. They use few-shot examples to guide LLM to do task parsing and planning.\nInstruction:')],
 'answer': 'Task decomposition is a technique used in artificial intelligence to break down complex tasks into smaller and more manageable subtasks. This approach helps agents or models to tackle difficult problems by dividing them into simpler steps, improving performance and interpretability. Different methods like Chain of Thought and Tree of Thoughts have been developed to enhance task decomposition in AI systems.'}
```


:::tip

Check out the [LangSmith trace](https://smith.langchain.com/public/1c055a3b-0236-4670-a3fb-023d418ba796/r)

:::

## Structure sources in model response

Up to this point, we've simply propagated the documents returned from the retrieval step through to the final response. But this may not illustrate what subset of information the model relied on when generating its answer. Below, we show how to structure sources into the model response, allowing the model to report what specific context it relied on for its answer.

Because the above LCEL implementation is composed of [Runnable](/docs/concepts/#runnable-interface) primitives, it is straightforward to extend. Below, we make a simple change:

- We use the model's tool-calling features to generate [structured output](/docs/how_to/structured_output/), consisting of an answer and list of sources. The schema for the response is represented in the `AnswerWithSources` TypedDict, below.
- We remove the `StrOutputParser()`, as we expect `dict` output in this scenario.


```python
<!--IMPORTS:[{"imported": "RunnablePassthrough", "source": "langchain_core.runnables", "docs": "https://api.python.langchain.com/en/latest/runnables/langchain_core.runnables.passthrough.RunnablePassthrough.html", "title": "How to get your RAG application to return sources"}]-->
from typing import List

from langchain_core.runnables import RunnablePassthrough
from typing_extensions import Annotated, TypedDict


# Desired schema for response
class AnswerWithSources(TypedDict):
    """An answer to the question, with sources."""

    answer: str
    sources: Annotated[
        List[str],
        ...,
        "List of sources (author + year) used to answer the question",
    ]


# Our rag_chain_from_docs has the following changes:
# - add `.with_structured_output` to the LLM;
# - remove the output parser
rag_chain_from_docs = (
    {
        "input": lambda x: x["input"],
        "context": lambda x: format_docs(x["context"]),
    }
    | prompt
    | llm.with_structured_output(AnswerWithSources)
)

retrieve_docs = (lambda x: x["input"]) | retriever

chain = RunnablePassthrough.assign(context=retrieve_docs).assign(
    answer=rag_chain_from_docs
)

response = chain.invoke({"input": "What is Chain of Thought?"})
```


```python
import json

print(json.dumps(response["answer"], indent=2))
```
```output
{
  "answer": "Chain of Thought (CoT) is a prompting technique that enhances model performance on complex tasks by instructing the model to \"think step by step\" to decompose hard tasks into smaller and simpler steps. It transforms big tasks into multiple manageable tasks and sheds light on the interpretation of the model's thinking process.",
  "sources": [
    "Wei et al. 2022"
  ]
}
```
:::tip

View [LangSmith trace](https://smith.langchain.com/public/0eeddf06-3a7b-4f27-974c-310ca8160f60/r)

:::