---
canonical: https://python.langchain.com/v0.2/docs/integrations/document_loaders/spider/
custom_edit_url: https://github.com/langchain-ai/langchain/edit/master/docs/docs/integrations/document_loaders/spider.ipynb
---

# Spider
[Spider](https://spider.cloud/) is the [fastest](https://github.com/spider-rs/spider/blob/main/benches/BENCHMARKS.md) and most affordable crawler and scraper that returns LLM-ready data.

## Setup


```python
pip install spider-client
```

## Usage
To use spider you need to have an API key from [spider.cloud](https://spider.cloud/).


```python
<!--IMPORTS:[{"imported": "SpiderLoader", "source": "langchain_community.document_loaders", "docs": "https://api.python.langchain.com/en/latest/document_loaders/langchain_community.document_loaders.spider.SpiderLoader.html", "title": "Spider"}]-->
from langchain_community.document_loaders import SpiderLoader

loader = SpiderLoader(
    api_key="YOUR_API_KEY",
    url="https://spider.cloud",
    mode="scrape",  # if no API key is provided it looks for SPIDER_API_KEY in env
)

data = loader.load()
print(data)
```
```output
[Document(page_content='Spider - Fastest Web Crawler built for AI Agents and Large Language Models[Spider v1 Logo Spider ](/)The World\'s Fastest and Cheapest Crawler API==========View Demo* Basic* StreamingExample requestPythonCopy```import requests, osheaders = {    \'Authorization\': os.environ["SPIDER_API_KEY"],    \'Content-Type\': \'application/json\',}json_data = {"limit":50,"url":"http://www.example.com"}response = requests.post(\'https://api.spider.cloud/crawl\',  headers=headers,  json=json_data)print(response.json())```Example ResponseScrape with no headaches----------* Proxy rotations* Agent headers* Avoid anti-bot detections* Headless chrome* Markdown LLM ResponsesThe Fastest Web Crawler----------* Powered by [spider-rs](https://github.com/spider-rs/spider)* Do 20,000 pages in seconds* Full concurrency* Powerful and simple API* Cost effectiveScrape Anything with AI----------* Custom scripting browser* Custom data extraction* Data pipelines* Detailed insights* Advanced labeling[API](/docs/api) [Price](/credits/new) [Guides](/guides) [About](/about) [Docs](https://docs.rs/spider/latest/spider/) [Privacy](/privacy) [Terms](/eula)© 2024 Spider from A11yWatchTheme Light Dark Toggle Theme [GitHubGithub](https://github.com/spider-rs/spider)', metadata={'description': 'Collect data rapidly from any website. Seamlessly scrape websites and get data tailored for LLM workloads.', 'domain': 'spider.cloud', 'extracted_data': None, 'file_size': 33743, 'keywords': None, 'pathname': '/', 'resource_type': 'html', 'title': 'Spider - Fastest Web Crawler built for AI Agents and Large Language Models', 'url': '48f1bc3c-3fbb-408a-865b-c191a1bb1f48/spider.cloud/index.html', 'user_id': '48f1bc3c-3fbb-408a-865b-c191a1bb1f48'})]
```
## Modes
- `scrape`: Default mode that scrapes a single URL
- `crawl`: Crawl all subpages of the domain url provided

## Crawler options
The `params` parameter is a dictionary that can be passed to the loader. See the [Spider documentation](https://spider.cloud/docs/api) to see all available parameters


## Related

- Document loader [conceptual guide](/docs/concepts/#document-loaders)
- Document loader [how-to guides](/docs/how_to/#document-loaders)