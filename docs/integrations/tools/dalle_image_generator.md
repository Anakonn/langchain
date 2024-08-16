---
canonical: https://python.langchain.com/v0.2/docs/integrations/tools/dalle_image_generator/
custom_edit_url: https://github.com/langchain-ai/langchain/edit/master/docs/docs/integrations/tools/dalle_image_generator.ipynb
---

# Dall-E Image Generator

>[OpenAI Dall-E](https://openai.com/dall-e-3) are text-to-image models developed by `OpenAI` using deep learning methodologies to generate digital images from natural language descriptions, called "prompts".

This notebook shows how you can generate images from a prompt synthesized using an OpenAI LLM. The images are generated using `Dall-E`, which uses the same OpenAI API key as the LLM.


```python
# Needed if you would like to display images in the notebook
%pip install --upgrade --quiet  opencv-python scikit-image langchain-community
```


```python
<!--IMPORTS:[{"imported": "OpenAI", "source": "langchain_openai", "docs": "https://api.python.langchain.com/en/latest/llms/langchain_openai.llms.base.OpenAI.html", "title": "Dall-E Image Generator"}]-->
import os

from langchain_openai import OpenAI

os.environ["OPENAI_API_KEY"] = "<your-key-here>"
```

## Run as a chain


```python
<!--IMPORTS:[{"imported": "LLMChain", "source": "langchain.chains", "docs": "https://api.python.langchain.com/en/latest/chains/langchain.chains.llm.LLMChain.html", "title": "Dall-E Image Generator"}, {"imported": "DallEAPIWrapper", "source": "langchain_community.utilities.dalle_image_generator", "docs": "https://api.python.langchain.com/en/latest/utilities/langchain_community.utilities.dalle_image_generator.DallEAPIWrapper.html", "title": "Dall-E Image Generator"}, {"imported": "PromptTemplate", "source": "langchain_core.prompts", "docs": "https://api.python.langchain.com/en/latest/prompts/langchain_core.prompts.prompt.PromptTemplate.html", "title": "Dall-E Image Generator"}, {"imported": "OpenAI", "source": "langchain_openai", "docs": "https://api.python.langchain.com/en/latest/llms/langchain_openai.llms.base.OpenAI.html", "title": "Dall-E Image Generator"}]-->
from langchain.chains import LLMChain
from langchain_community.utilities.dalle_image_generator import DallEAPIWrapper
from langchain_core.prompts import PromptTemplate
from langchain_openai import OpenAI

llm = OpenAI(temperature=0.9)
prompt = PromptTemplate(
    input_variables=["image_desc"],
    template="Generate a detailed prompt to generate an image based on the following description: {image_desc}",
)
chain = LLMChain(llm=llm, prompt=prompt)
```


```python
image_url = DallEAPIWrapper().run(chain.run("halloween night at a haunted museum"))
```


```python
image_url
```



```output
'https://oaidalleapiprodscus.blob.core.windows.net/private/org-i0zjYONU3PemzJ222esBaAzZ/user-f6uEIOFxoiUZivy567cDSWni/img-i7Z2ZxvJ4IbbdAiO6OXJgS3v.png?st=2023-08-11T14%3A03%3A14Z&se=2023-08-11T16%3A03%3A14Z&sp=r&sv=2021-08-06&sr=b&rscd=inline&rsct=image/png&skoid=6aaadede-4fb3-4698-a8f6-684d7786b067&sktid=a48cca56-e6da-484e-a814-9c849652bcb3&skt=2023-08-10T20%3A58%3A32Z&ske=2023-08-11T20%3A58%3A32Z&sks=b&skv=2021-08-06&sig=/sECe7C0EAq37ssgBm7g7JkVIM/Q1W3xOstd0Go6slA%3D'
```



```python
# You can click on the link above to display the image
# Or you can try the options below to display the image inline in this notebook

try:
    import google.colab

    IN_COLAB = True
except ImportError:
    IN_COLAB = False

if IN_COLAB:
    from google.colab.patches import cv2_imshow  # for image display
    from skimage import io

    image = io.imread(image_url)
    cv2_imshow(image)
else:
    import cv2
    from skimage import io

    image = io.imread(image_url)
    cv2.imshow("image", image)
    cv2.waitKey(0)  # wait for a keyboard input
    cv2.destroyAllWindows()
```

## Run as a tool with an agent


```python
<!--IMPORTS:[{"imported": "initialize_agent", "source": "langchain.agents", "docs": "https://api.python.langchain.com/en/latest/agents/langchain.agents.initialize.initialize_agent.html", "title": "Dall-E Image Generator"}, {"imported": "load_tools", "source": "langchain.agents", "docs": "https://api.python.langchain.com/en/latest/agent_toolkits/langchain_community.agent_toolkits.load_tools.load_tools.html", "title": "Dall-E Image Generator"}]-->
from langchain.agents import initialize_agent, load_tools

tools = load_tools(["dalle-image-generator"])
agent = initialize_agent(tools, llm, agent="zero-shot-react-description", verbose=True)
output = agent.run("Create an image of a halloween night at a haunted museum")
```
```output


[1m> Entering new AgentExecutor chain...[0m
[32;1m[1;3m What is the best way to turn this description into an image?
Action: Dall-E Image Generator
Action Input: A spooky Halloween night at a haunted museum[0mhttps://oaidalleapiprodscus.blob.core.windows.net/private/org-rocrupyvzgcl4yf25rqq6d1v/user-WsxrbKyP2c8rfhCKWDyMfe8N/img-ogKfqxxOS5KWVSj4gYySR6FY.png?st=2023-01-31T07%3A38%3A25Z&se=2023-01-31T09%3A38%3A25Z&sp=r&sv=2021-08-06&sr=b&rscd=inline&rsct=image/png&skoid=6aaadede-4fb3-4698-a8f6-684d7786b067&sktid=a48cca56-e6da-484e-a814-9c849652bcb3&skt=2023-01-30T22%3A19%3A36Z&ske=2023-01-31T22%3A19%3A36Z&sks=b&skv=2021-08-06&sig=XsomxxBfu2CP78SzR9lrWUlbask4wBNnaMsHamy4VvU%3D

Observation: [36;1m[1;3mhttps://oaidalleapiprodscus.blob.core.windows.net/private/org-rocrupyvzgcl4yf25rqq6d1v/user-WsxrbKyP2c8rfhCKWDyMfe8N/img-ogKfqxxOS5KWVSj4gYySR6FY.png?st=2023-01-31T07%3A38%3A25Z&se=2023-01-31T09%3A38%3A25Z&sp=r&sv=2021-08-06&sr=b&rscd=inline&rsct=image/png&skoid=6aaadede-4fb3-4698-a8f6-684d7786b067&sktid=a48cca56-e6da-484e-a814-9c849652bcb3&skt=2023-01-30T22%3A19%3A36Z&ske=2023-01-31T22%3A19%3A36Z&sks=b&skv=2021-08-06&sig=XsomxxBfu2CP78SzR9lrWUlbask4wBNnaMsHamy4VvU%3D[0m
Thought:[32;1m[1;3m With the image generated, I can now make my final answer.
Final Answer: An image of a Halloween night at a haunted museum can be seen here: https://oaidalleapiprodscus.blob.core.windows.net/private/org-rocrupyvzgcl4yf25rqq6d1v/user-WsxrbKyP2c8rfhCKWDyMfe8N/img-ogKfqxxOS5KWVSj4gYySR6FY.png?st=2023-01-31T07%3A38%3A25Z&se=2023-01-31T09%3A38%3A25Z&sp=r&sv=2021-08-06&sr=b&rscd=inline&rsct=image/png&skoid=6aaadede-4fb3-4698-a8f6-684d7786b067&sktid=a48cca56-e6da-484e-a814-9c849652bcb3&skt=2023-01-30T22[0m

[1m> Finished chain.[0m
```

## Related

- Tool [conceptual guide](/docs/concepts/#tools)
- Tool [how-to guides](/docs/how_to/#tools)