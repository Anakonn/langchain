---
canonical: https://python.langchain.com/v0.2/docs/how_to/tools_as_openai_functions/
custom_edit_url: https://github.com/langchain-ai/langchain/edit/master/docs/docs/how_to/tools_as_openai_functions.ipynb
---

# How to convert tools to OpenAI Functions

This notebook goes over how to use LangChain tools as OpenAI functions.


```python
%pip install -qU langchain-community langchain-openai
```


```python
<!--IMPORTS:[{"imported": "MoveFileTool", "source": "langchain_community.tools", "docs": "https://api.python.langchain.com/en/latest/tools/langchain_community.tools.file_management.move.MoveFileTool.html", "title": "How to convert tools to OpenAI Functions"}, {"imported": "HumanMessage", "source": "langchain_core.messages", "docs": "https://api.python.langchain.com/en/latest/messages/langchain_core.messages.human.HumanMessage.html", "title": "How to convert tools to OpenAI Functions"}, {"imported": "convert_to_openai_function", "source": "langchain_core.utils.function_calling", "docs": "https://api.python.langchain.com/en/latest/utils/langchain_core.utils.function_calling.convert_to_openai_function.html", "title": "How to convert tools to OpenAI Functions"}, {"imported": "ChatOpenAI", "source": "langchain_openai", "docs": "https://api.python.langchain.com/en/latest/chat_models/langchain_openai.chat_models.base.ChatOpenAI.html", "title": "How to convert tools to OpenAI Functions"}]-->
from langchain_community.tools import MoveFileTool
from langchain_core.messages import HumanMessage
from langchain_core.utils.function_calling import convert_to_openai_function
from langchain_openai import ChatOpenAI
```


```python
model = ChatOpenAI(model="gpt-3.5-turbo")
```


```python
tools = [MoveFileTool()]
functions = [convert_to_openai_function(t) for t in tools]
```


```python
functions[0]
```



```output
{'name': 'move_file',
 'description': 'Move or rename a file from one location to another',
 'parameters': {'type': 'object',
  'properties': {'source_path': {'description': 'Path of the file to move',
    'type': 'string'},
   'destination_path': {'description': 'New path for the moved file',
    'type': 'string'}},
  'required': ['source_path', 'destination_path']}}
```



```python
message = model.invoke(
    [HumanMessage(content="move file foo to bar")], functions=functions
)
```


```python
message
```



```output
AIMessage(content='', additional_kwargs={'function_call': {'arguments': '{\n  "source_path": "foo",\n  "destination_path": "bar"\n}', 'name': 'move_file'}})
```



```python
message.additional_kwargs["function_call"]
```



```output
{'name': 'move_file',
 'arguments': '{\n  "source_path": "foo",\n  "destination_path": "bar"\n}'}
```


With OpenAI chat models we can also automatically bind and convert function-like objects with `bind_functions`


```python
model_with_functions = model.bind_functions(tools)
model_with_functions.invoke([HumanMessage(content="move file foo to bar")])
```



```output
AIMessage(content='', additional_kwargs={'function_call': {'arguments': '{\n  "source_path": "foo",\n  "destination_path": "bar"\n}', 'name': 'move_file'}})
```


Or we can use the update OpenAI API that uses `tools` and `tool_choice` instead of `functions` and `function_call` by using `ChatOpenAI.bind_tools`:


```python
model_with_tools = model.bind_tools(tools)
model_with_tools.invoke([HumanMessage(content="move file foo to bar")])
```



```output
AIMessage(content='', additional_kwargs={'tool_calls': [{'id': 'call_btkY3xV71cEVAOHnNa5qwo44', 'function': {'arguments': '{\n  "source_path": "foo",\n  "destination_path": "bar"\n}', 'name': 'move_file'}, 'type': 'function'}]})
```