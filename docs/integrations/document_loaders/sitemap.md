---
canonical: https://python.langchain.com/v0.2/docs/integrations/document_loaders/sitemap/
custom_edit_url: https://github.com/langchain-ai/langchain/edit/master/docs/docs/integrations/document_loaders/sitemap.ipynb
---

# Sitemap

Extends from the `WebBaseLoader`, `SitemapLoader` loads a sitemap from a given URL, and then scrapes and loads all pages in the sitemap, returning each page as a Document.

The scraping is done concurrently. There are reasonable limits to concurrent requests, defaulting to 2 per second.  If you aren't concerned about being a good citizen, or you control the scrapped server, or don't care about load you can increase this limit. Note, while this will speed up the scraping process, it may cause the server to block you. Be careful!

## Overview
### Integration details

| Class | Package | Local | Serializable | [JS support](https://js.langchain.com/v0.2/docs/integrations/document_loaders/web_loaders/sitemap/)|
| :--- | :--- | :---: | :---: |  :---: |
| [SiteMapLoader](https://api.python.langchain.com/en/latest/document_loaders/langchain_community.document_loaders.sitemap.SitemapLoader.html#langchain_community.document_loaders.sitemap.SitemapLoader) | [langchain_community](https://api.python.langchain.com/en/latest/community_api_reference.html) | ✅ | ❌ | ✅ | 
### Loader features
| Source | Document Lazy Loading | Native Async Support
| :---: | :---: | :---: | 
| SiteMapLoader | ✅ | ❌ | 

## Setup

To access SiteMap document loader you'll need to install the `langchain-community` integration package.

### Credentials

No credentials are needed to run this.

If you want to get automated best in-class tracing of your model calls you can also set your [LangSmith](https://docs.smith.langchain.com/) API key by uncommenting below:


```python
# os.environ["LANGSMITH_API_KEY"] = getpass.getpass("Enter your LangSmith API key: ")
# os.environ["LANGSMITH_TRACING"] = "true"
```

### Installation

Install **langchain_community**.


```python
%pip install -qU langchain-community
```

### Fix notebook asyncio bug


```python
import nest_asyncio

nest_asyncio.apply()
```

## Initialization

Now we can instantiate our model object and load documents:


```python
<!--IMPORTS:[{"imported": "SitemapLoader", "source": "langchain_community.document_loaders.sitemap", "docs": "https://api.python.langchain.com/en/latest/document_loaders/langchain_community.document_loaders.sitemap.SitemapLoader.html", "title": "Sitemap"}]-->
from langchain_community.document_loaders.sitemap import SitemapLoader
```


```python
sitemap_loader = SitemapLoader(web_path="https://api.python.langchain.com/sitemap.xml")
```

## Load


```python
docs = sitemap_loader.load()
docs[0]
```
```output
Fetching pages: 100%|##########| 28/28 [00:04<00:00,  6.83it/s]
```


```output
Document(metadata={'source': 'https://api.python.langchain.com/en/stable/', 'loc': 'https://api.python.langchain.com/en/stable/', 'lastmod': '2024-05-15T00:29:42.163001+00:00', 'changefreq': 'weekly', 'priority': '1'}, page_content='\n\n\n\n\n\n\n\n\n\nLangChain Python API Reference Documentation.\n\n\nYou will be automatically redirected to the new location of this page.\n\n')
```



```python
print(docs[0].metadata)
```
```output
{'source': 'https://api.python.langchain.com/en/stable/', 'loc': 'https://api.python.langchain.com/en/stable/', 'lastmod': '2024-05-15T00:29:42.163001+00:00', 'changefreq': 'weekly', 'priority': '1'}
```
You can change the `requests_per_second` parameter to increase the max concurrent requests. and use `requests_kwargs` to pass kwargs when send requests.


```python
sitemap_loader.requests_per_second = 2
# Optional: avoid `[SSL: CERTIFICATE_VERIFY_FAILED]` issue
sitemap_loader.requests_kwargs = {"verify": False}
```

## Lazy Load

You can also load the pages lazily in order to minimize the memory load.


```python
page = []
for doc in sitemap_loader.lazy_load():
    page.append(doc)
    if len(page) >= 10:
        # do some paged operation, e.g.
        # index.upsert(page)

        page = []
```
```output
Fetching pages: 100%|##########| 28/28 [00:01<00:00, 19.06it/s]
```
## Filtering sitemap URLs

Sitemaps can be massive files, with thousands of URLs.  Often you don't need every single one of them.  You can filter the URLs by passing a list of strings or regex patterns to the `filter_urls` parameter.  Only URLs that match one of the patterns will be loaded.


```python
loader = SitemapLoader(
    web_path="https://api.python.langchain.com/sitemap.xml",
    filter_urls=["https://api.python.langchain.com/en/latest"],
)
documents = loader.load()
```


```python
documents[0]
```



```output
Document(page_content='\n\n\n\n\n\n\n\n\n\nLangChain Python API Reference Documentation.\n\n\nYou will be automatically redirected to the new location of this page.\n\n', metadata={'source': 'https://api.python.langchain.com/en/latest/', 'loc': 'https://api.python.langchain.com/en/latest/', 'lastmod': '2024-02-12T05:26:10.971077+00:00', 'changefreq': 'daily', 'priority': '0.9'})
```


## Add custom scraping rules

The `SitemapLoader` uses `beautifulsoup4` for the scraping process, and it scrapes every element on the page by default. The `SitemapLoader` constructor accepts a custom scraping function. This feature can be helpful to tailor the scraping process to your specific needs; for example, you might want to avoid scraping headers or navigation elements.

 The following example shows how to develop and use a custom function to avoid navigation and header elements.

Import the `beautifulsoup4` library and define the custom function.


```python
pip install beautifulsoup4
```


```python
from bs4 import BeautifulSoup


def remove_nav_and_header_elements(content: BeautifulSoup) -> str:
    # Find all 'nav' and 'header' elements in the BeautifulSoup object
    nav_elements = content.find_all("nav")
    header_elements = content.find_all("header")

    # Remove each 'nav' and 'header' element from the BeautifulSoup object
    for element in nav_elements + header_elements:
        element.decompose()

    return str(content.get_text())
```

Add your custom function to the `SitemapLoader` object.


```python
loader = SitemapLoader(
    "https://api.python.langchain.com/sitemap.xml",
    filter_urls=["https://api.python.langchain.com/en/latest/"],
    parsing_function=remove_nav_and_header_elements,
)
```

## Local Sitemap

The sitemap loader can also be used to load local files.


```python
sitemap_loader = SitemapLoader(web_path="example_data/sitemap.xml", is_local=True)

docs = sitemap_loader.load()
```

## API reference

For detailed documentation of all SiteMapLoader features and configurations head to the API reference: https://api.python.langchain.com/en/latest/document_loaders/langchain_community.document_loaders.sitemap.SitemapLoader.html#langchain_community.document_loaders.sitemap.SitemapLoader


## Related

- Document loader [conceptual guide](/docs/concepts/#document-loaders)
- Document loader [how-to guides](/docs/how_to/#document-loaders)