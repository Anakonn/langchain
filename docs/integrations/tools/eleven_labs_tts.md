---
canonical: https://python.langchain.com/v0.2/docs/integrations/tools/eleven_labs_tts/
custom_edit_url: https://github.com/langchain-ai/langchain/edit/master/docs/docs/integrations/tools/eleven_labs_tts.ipynb
---

# Eleven Labs Text2Speech

This notebook shows how to interact with the `ElevenLabs API` to achieve text-to-speech capabilities.

First, you need to set up an ElevenLabs account. You can follow the instructions [here](https://docs.elevenlabs.io/welcome/introduction).


```python
%pip install --upgrade --quiet  elevenlabs langchain-community
```


```python
import os

os.environ["ELEVEN_API_KEY"] = ""
```

## Usage


```python
<!--IMPORTS:[{"imported": "ElevenLabsText2SpeechTool", "source": "langchain_community.tools", "docs": "https://api.python.langchain.com/en/latest/tools/langchain_community.tools.eleven_labs.text2speech.ElevenLabsText2SpeechTool.html", "title": "Eleven Labs Text2Speech"}]-->
from langchain_community.tools import ElevenLabsText2SpeechTool

text_to_speak = "Hello world! I am the real slim shady"

tts = ElevenLabsText2SpeechTool()
tts.name
```



```output
'eleven_labs_text2speech'
```


We can generate audio, save it to the temporary file and then play it.


```python
speech_file = tts.run(text_to_speak)
tts.play(speech_file)
```

Or stream audio directly.


```python
tts.stream_speech(text_to_speak)
```

## Use within an Agent


```python
<!--IMPORTS:[{"imported": "AgentType", "source": "langchain.agents", "docs": "https://api.python.langchain.com/en/latest/agents/langchain.agents.agent_types.AgentType.html", "title": "Eleven Labs Text2Speech"}, {"imported": "initialize_agent", "source": "langchain.agents", "docs": "https://api.python.langchain.com/en/latest/agents/langchain.agents.initialize.initialize_agent.html", "title": "Eleven Labs Text2Speech"}, {"imported": "load_tools", "source": "langchain.agents", "docs": "https://api.python.langchain.com/en/latest/agent_toolkits/langchain_community.agent_toolkits.load_tools.load_tools.html", "title": "Eleven Labs Text2Speech"}, {"imported": "OpenAI", "source": "langchain_openai", "docs": "https://api.python.langchain.com/en/latest/llms/langchain_openai.llms.base.OpenAI.html", "title": "Eleven Labs Text2Speech"}]-->
from langchain.agents import AgentType, initialize_agent, load_tools
from langchain_openai import OpenAI
```


```python
llm = OpenAI(temperature=0)
tools = load_tools(["eleven_labs_text2speech"])
agent = initialize_agent(
    tools=tools,
    llm=llm,
    agent=AgentType.STRUCTURED_CHAT_ZERO_SHOT_REACT_DESCRIPTION,
    verbose=True,
)
```


```python
audio_file = agent.run("Tell me a joke and read it out for me.")
```
```output


[1m> Entering new AgentExecutor chain...[0m
[32;1m[1;3mAction:
```
{
  "action": "eleven_labs_text2speech",
  "action_input": {
    "query": "Why did the chicken cross the playground? To get to the other slide!"
  }
}
```

[0m
Observation: [36;1m[1;3m/tmp/tmpsfg783f1.wav[0m
Thought:[32;1m[1;3m I have the audio file ready to be sent to the human
Action:
```
{
  "action": "Final Answer",
  "action_input": "/tmp/tmpsfg783f1.wav"
}
```

[0m

[1m> Finished chain.[0m
```

```python
tts.play(audio_file)
```


## Related

- Tool [conceptual guide](/docs/concepts/#tools)
- Tool [how-to guides](/docs/how_to/#tools)