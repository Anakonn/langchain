---
canonical: https://python.langchain.com/v0.2/docs/how_to/output_parser_retry/
custom_edit_url: https://github.com/langchain-ai/langchain/edit/master/docs/docs/how_to/output_parser_retry.ipynb
---

# How to retry when a parsing error occurs

While in some cases it is possible to fix any parsing mistakes by only looking at the output, in other cases it isn't. An example of this is when the output is not just in the incorrect format, but is partially complete. Consider the below example.


```python
<!--IMPORTS:[{"imported": "OutputFixingParser", "source": "langchain.output_parsers", "docs": "https://api.python.langchain.com/en/latest/output_parsers/langchain.output_parsers.fix.OutputFixingParser.html", "title": "How to retry when a parsing error occurs"}, {"imported": "PydanticOutputParser", "source": "langchain_core.output_parsers", "docs": "https://api.python.langchain.com/en/latest/output_parsers/langchain_core.output_parsers.pydantic.PydanticOutputParser.html", "title": "How to retry when a parsing error occurs"}, {"imported": "PromptTemplate", "source": "langchain_core.prompts", "docs": "https://api.python.langchain.com/en/latest/prompts/langchain_core.prompts.prompt.PromptTemplate.html", "title": "How to retry when a parsing error occurs"}, {"imported": "ChatOpenAI", "source": "langchain_openai", "docs": "https://api.python.langchain.com/en/latest/chat_models/langchain_openai.chat_models.base.ChatOpenAI.html", "title": "How to retry when a parsing error occurs"}, {"imported": "OpenAI", "source": "langchain_openai", "docs": "https://api.python.langchain.com/en/latest/llms/langchain_openai.llms.base.OpenAI.html", "title": "How to retry when a parsing error occurs"}]-->
from langchain.output_parsers import OutputFixingParser
from langchain_core.output_parsers import PydanticOutputParser
from langchain_core.prompts import PromptTemplate
from langchain_core.pydantic_v1 import BaseModel, Field
from langchain_openai import ChatOpenAI, OpenAI
```


```python
template = """Based on the user question, provide an Action and Action Input for what step should be taken.
{format_instructions}
Question: {query}
Response:"""


class Action(BaseModel):
    action: str = Field(description="action to take")
    action_input: str = Field(description="input to the action")


parser = PydanticOutputParser(pydantic_object=Action)
```


```python
prompt = PromptTemplate(
    template="Answer the user query.\n{format_instructions}\n{query}\n",
    input_variables=["query"],
    partial_variables={"format_instructions": parser.get_format_instructions()},
)
```


```python
prompt_value = prompt.format_prompt(query="who is leo di caprios gf?")
```


```python
bad_response = '{"action": "search"}'
```

If we try to parse this response as is, we will get an error:


```python
parser.parse(bad_response)
```

```output
---------------------------------------------------------------------------
``````output
ValidationError                           Traceback (most recent call last)
``````output
File ~/workplace/langchain/libs/langchain/langchain/output_parsers/pydantic.py:30, in PydanticOutputParser.parse(self, text)
     29     json_object = json.loads(json_str, strict=False)
---> 30     return self.pydantic_object.parse_obj(json_object)
     32 except (json.JSONDecodeError, ValidationError) as e:
``````output
File ~/.pyenv/versions/3.10.1/envs/langchain/lib/python3.10/site-packages/pydantic/main.py:526, in pydantic.main.BaseModel.parse_obj()
``````output
File ~/.pyenv/versions/3.10.1/envs/langchain/lib/python3.10/site-packages/pydantic/main.py:341, in pydantic.main.BaseModel.__init__()
``````output
ValidationError: 1 validation error for Action
action_input
  field required (type=value_error.missing)
``````output

During handling of the above exception, another exception occurred:
``````output
OutputParserException                     Traceback (most recent call last)
``````output
Cell In[6], line 1
----> 1 parser.parse(bad_response)
``````output
File ~/workplace/langchain/libs/langchain/langchain/output_parsers/pydantic.py:35, in PydanticOutputParser.parse(self, text)
     33 name = self.pydantic_object.__name__
     34 msg = f"Failed to parse {name} from completion {text}. Got: {e}"
---> 35 raise OutputParserException(msg, llm_output=text)
``````output
OutputParserException: Failed to parse Action from completion {"action": "search"}. Got: 1 validation error for Action
action_input
  field required (type=value_error.missing)
```

If we try to use the `OutputFixingParser` to fix this error, it will be confused - namely, it doesn't know what to actually put for action input.


```python
fix_parser = OutputFixingParser.from_llm(parser=parser, llm=ChatOpenAI())
```


```python
fix_parser.parse(bad_response)
```



```output
Action(action='search', action_input='input')
```


Instead, we can use the RetryOutputParser, which passes in the prompt (as well as the original output) to try again to get a better response.


```python
<!--IMPORTS:[{"imported": "RetryOutputParser", "source": "langchain.output_parsers", "docs": "https://api.python.langchain.com/en/latest/output_parsers/langchain.output_parsers.retry.RetryOutputParser.html", "title": "How to retry when a parsing error occurs"}]-->
from langchain.output_parsers import RetryOutputParser
```


```python
retry_parser = RetryOutputParser.from_llm(parser=parser, llm=OpenAI(temperature=0))
```


```python
retry_parser.parse_with_prompt(bad_response, prompt_value)
```



```output
Action(action='search', action_input='leo di caprio girlfriend')
```


We can also add the RetryOutputParser easily with a custom chain which transform the raw LLM/ChatModel output into a more workable format.


```python
<!--IMPORTS:[{"imported": "RunnableLambda", "source": "langchain_core.runnables", "docs": "https://api.python.langchain.com/en/latest/runnables/langchain_core.runnables.base.RunnableLambda.html", "title": "How to retry when a parsing error occurs"}, {"imported": "RunnableParallel", "source": "langchain_core.runnables", "docs": "https://api.python.langchain.com/en/latest/runnables/langchain_core.runnables.base.RunnableParallel.html", "title": "How to retry when a parsing error occurs"}]-->
from langchain_core.runnables import RunnableLambda, RunnableParallel

completion_chain = prompt | OpenAI(temperature=0)

main_chain = RunnableParallel(
    completion=completion_chain, prompt_value=prompt
) | RunnableLambda(lambda x: retry_parser.parse_with_prompt(**x))


main_chain.invoke({"query": "who is leo di caprios gf?"})
```
```output
Action(action='search', action_input='leo di caprio girlfriend')
```
Find out api documentation for [RetryOutputParser](https://api.python.langchain.com/en/latest/output_parsers/langchain.output_parsers.retry.RetryOutputParser.html#langchain.output_parsers.retry.RetryOutputParser).