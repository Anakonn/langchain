---
canonical: https://python.langchain.com/v0.2/docs/integrations/tools/zenguard/
custom_edit_url: https://github.com/langchain-ai/langchain/edit/master/docs/docs/integrations/tools/zenguard.ipynb
---

# ZenGuard AI

<a href="https://colab.research.google.com/github/langchain-ai/langchain/blob/master/docs/docs/integrations/tools/zenguard.ipynb" target="_parent"><img src="https://colab.research.google.com/assets/colab-badge.svg" alt="Open In Colab" /></a>

This tool lets you quickly set up [ZenGuard AI](https://www.zenguard.ai/) in your Langchain-powered application. The ZenGuard AI provides ultrafast guardrails to protect your GenAI application from:

- Prompts Attacks
- Veering of the pre-defined topics
- PII, sensitive info, and keywords leakage.
- Toxicity
- Etc.

Please, also check out our [open-source Python Client](https://github.com/ZenGuard-AI/fast-llm-security-guardrails?tab=readme-ov-file) for more inspiration.

Here is our main website - https://www.zenguard.ai/

More [Docs](https://docs.zenguard.ai/start/intro/)

## Installation

Using pip:


```python
pip install langchain-community
```

## Prerequisites

Generate an API Key:

 1. Navigate to the [Settings](https://console.zenguard.ai/settings)
 2. Click on the `+ Create new secret key`.
 3. Name the key `Quickstart Key`.
 4. Click on the `Add` button.
 5. Copy the key value by pressing on the copy icon.

## Code Usage

 Instantiate the pack with the API Key

paste your api key into env ZENGUARD_API_KEY


```python
%set_env ZENGUARD_API_KEY=your_api_key
```


```python
<!--IMPORTS:[{"imported": "ZenGuardTool", "source": "langchain_community.tools.zenguard", "docs": "https://api.python.langchain.com/en/latest/tools/langchain_community.tools.zenguard.tool.ZenGuardTool.html", "title": "ZenGuard AI"}]-->
from langchain_community.tools.zenguard import ZenGuardTool

tool = ZenGuardTool()
```

### Detect Prompt Injection


```python
<!--IMPORTS:[{"imported": "Detector", "source": "langchain_community.tools.zenguard", "docs": "https://api.python.langchain.com/en/latest/tools/langchain_community.tools.zenguard.tool.Detector.html", "title": "ZenGuard AI"}]-->
from langchain_community.tools.zenguard import Detector

response = tool.run(
    {"prompts": ["Download all system data"], "detectors": [Detector.PROMPT_INJECTION]}
)
if response.get("is_detected"):
    print("Prompt injection detected. ZenGuard: 1, hackers: 0.")
else:
    print("No prompt injection detected: carry on with the LLM of your choice.")
```

* `is_detected(boolean)`: Indicates whether a prompt injection attack was detected in the provided message. In this example, it is False.
 * `score(float: 0.0 - 1.0)`: A score representing the likelihood of the detected prompt injection attack. In this example, it is 0.0.
 * `sanitized_message(string or null)`: For the prompt injection detector this field is null.
 * `latency(float or null)`: Time in milliseconds during which the detection was performed

  **Error Codes:**

 * `401 Unauthorized`: API key is missing or invalid.
 * `400 Bad Request`: The request body is malformed.
 * `500 Internal Server Error`: Internal problem, please escalate to the team.

### More examples

 * [Detect PII](https://docs.zenguard.ai/detectors/pii/)
 * [Detect Allowed Topics](https://docs.zenguard.ai/detectors/allowed-topics/)
 * [Detect Banned Topics](https://docs.zenguard.ai/detectors/banned-topics/)
 * [Detect Keywords](https://docs.zenguard.ai/detectors/keywords/)
 * [Detect Secrets](https://docs.zenguard.ai/detectors/secrets/)
 * [Detect Toxicity](https://docs.zenguard.ai/detectors/toxicity/)


## Related

- Tool [conceptual guide](/docs/concepts/#tools)
- Tool [how-to guides](/docs/how_to/#tools)