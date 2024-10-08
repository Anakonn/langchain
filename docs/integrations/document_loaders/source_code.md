---
canonical: https://python.langchain.com/v0.2/docs/integrations/document_loaders/source_code/
custom_edit_url: https://github.com/langchain-ai/langchain/edit/master/docs/docs/integrations/document_loaders/source_code.ipynb
---

# Source Code

This notebook covers how to load source code files using a special approach with language parsing: each top-level function and class in the code is loaded into separate documents. Any remaining code top-level code outside the already loaded functions and classes will be loaded into a separate document.

This approach can potentially improve the accuracy of QA models over source code.

The supported languages for code parsing are:

- C (*)
- C++ (*)
- C# (*)
- COBOL
- Elixir
- Go (*)
- Java (*)
- JavaScript (requires package `esprima`)
- Kotlin (*)
- Lua (*)
- Perl (*)
- Python
- Ruby (*)
- Rust (*)
- Scala (*)
- TypeScript (*)

Items marked with (*) require the packages `tree_sitter` and `tree_sitter_languages`.
It is straightforward to add support for additional languages using `tree_sitter`,
although this currently requires modifying LangChain.

The language used for parsing can be configured, along with the minimum number of
lines required to activate the splitting based on syntax.

If a language is not explicitly specified, `LanguageParser` will infer one from
filename extensions, if present.


```python
%pip install -qU esprima esprima tree_sitter tree_sitter_languages
```


```python
<!--IMPORTS:[{"imported": "GenericLoader", "source": "langchain_community.document_loaders.generic", "docs": "https://api.python.langchain.com/en/latest/document_loaders/langchain_community.document_loaders.generic.GenericLoader.html", "title": "Source Code"}, {"imported": "LanguageParser", "source": "langchain_community.document_loaders.parsers", "docs": "https://api.python.langchain.com/en/latest/document_loaders/langchain_community.document_loaders.parsers.language.language_parser.LanguageParser.html", "title": "Source Code"}, {"imported": "Language", "source": "langchain_text_splitters", "docs": "https://api.python.langchain.com/en/latest/base/langchain_text_splitters.base.Language.html", "title": "Source Code"}]-->
import warnings

warnings.filterwarnings("ignore")
from pprint import pprint

from langchain_community.document_loaders.generic import GenericLoader
from langchain_community.document_loaders.parsers import LanguageParser
from langchain_text_splitters import Language
```


```python
loader = GenericLoader.from_filesystem(
    "./example_data/source_code",
    glob="*",
    suffixes=[".py", ".js"],
    parser=LanguageParser(),
)
docs = loader.load()
```


```python
len(docs)
```



```output
6
```



```python
for document in docs:
    pprint(document.metadata)
```
```output
{'content_type': 'functions_classes',
 'language': <Language.PYTHON: 'python'>,
 'source': 'example_data/source_code/example.py'}
{'content_type': 'functions_classes',
 'language': <Language.PYTHON: 'python'>,
 'source': 'example_data/source_code/example.py'}
{'content_type': 'simplified_code',
 'language': <Language.PYTHON: 'python'>,
 'source': 'example_data/source_code/example.py'}
{'content_type': 'functions_classes',
 'language': <Language.JS: 'js'>,
 'source': 'example_data/source_code/example.js'}
{'content_type': 'functions_classes',
 'language': <Language.JS: 'js'>,
 'source': 'example_data/source_code/example.js'}
{'content_type': 'simplified_code',
 'language': <Language.JS: 'js'>,
 'source': 'example_data/source_code/example.js'}
```

```python
print("\n\n--8<--\n\n".join([document.page_content for document in docs]))
```
```output
class MyClass:
    def __init__(self, name):
        self.name = name

    def greet(self):
        print(f"Hello, {self.name}!")

--8<--

def main():
    name = input("Enter your name: ")
    obj = MyClass(name)
    obj.greet()

--8<--

# Code for: class MyClass:


# Code for: def main():


if __name__ == "__main__":
    main()

--8<--

class MyClass {
  constructor(name) {
    this.name = name;
  }

  greet() {
    console.log(`Hello, ${this.name}!`);
  }
}

--8<--

function main() {
  const name = prompt("Enter your name:");
  const obj = new MyClass(name);
  obj.greet();
}

--8<--

// Code for: class MyClass {

// Code for: function main() {

main();
```
The parser can be disabled for small files. 

The parameter `parser_threshold` indicates the minimum number of lines that the source code file must have to be segmented using the parser.


```python
loader = GenericLoader.from_filesystem(
    "./example_data/source_code",
    glob="*",
    suffixes=[".py"],
    parser=LanguageParser(language=Language.PYTHON, parser_threshold=1000),
)
docs = loader.load()
```


```python
len(docs)
```



```output
1
```



```python
print(docs[0].page_content)
```
```output
class MyClass:
    def __init__(self, name):
        self.name = name

    def greet(self):
        print(f"Hello, {self.name}!")


def main():
    name = input("Enter your name: ")
    obj = MyClass(name)
    obj.greet()


if __name__ == "__main__":
    main()
```
## Splitting

Additional splitting could be needed for those functions, classes, or scripts that are too big.


```python
loader = GenericLoader.from_filesystem(
    "./example_data/source_code",
    glob="*",
    suffixes=[".js"],
    parser=LanguageParser(language=Language.JS),
)
docs = loader.load()
```


```python
<!--IMPORTS:[{"imported": "Language", "source": "langchain_text_splitters", "docs": "https://api.python.langchain.com/en/latest/base/langchain_text_splitters.base.Language.html", "title": "Source Code"}, {"imported": "RecursiveCharacterTextSplitter", "source": "langchain_text_splitters", "docs": "https://api.python.langchain.com/en/latest/character/langchain_text_splitters.character.RecursiveCharacterTextSplitter.html", "title": "Source Code"}]-->
from langchain_text_splitters import (
    Language,
    RecursiveCharacterTextSplitter,
)
```


```python
js_splitter = RecursiveCharacterTextSplitter.from_language(
    language=Language.JS, chunk_size=60, chunk_overlap=0
)
```


```python
result = js_splitter.split_documents(docs)
```


```python
len(result)
```



```output
7
```



```python
print("\n\n--8<--\n\n".join([document.page_content for document in result]))
```
```output
class MyClass {
  constructor(name) {
    this.name = name;

--8<--

}

--8<--

greet() {
    console.log(`Hello, ${this.name}!`);
  }
}

--8<--

function main() {
  const name = prompt("Enter your name:");

--8<--

const obj = new MyClass(name);
  obj.greet();
}

--8<--

// Code for: class MyClass {

// Code for: function main() {

--8<--

main();
```
## Adding Languages using Tree-sitter Template

Expanding language support using the Tree-Sitter template involves a few essential steps:

1. **Creating a New Language File**:
    - Begin by creating a new file in the designated directory (langchain/libs/community/langchain_community/document_loaders/parsers/language).
    - Model this file based on the structure and parsing logic of existing language files like **`cpp.py`**.
    - You will also need to create a file in the langchain directory (langchain/libs/langchain/langchain/document_loaders/parsers/language).
2. **Parsing Language Specifics**:
    - Mimic the structure used in the **`cpp.py`** file, adapting it to suit the language you are incorporating.
    - The primary alteration involves adjusting the chunk query array to suit the syntax and structure of the language you are parsing.
3. **Testing the Language Parser**:
    - For thorough validation, generate a test file specific to the new language. Create **`test_language.py`** in the designated directory(langchain/libs/community/tests/unit_tests/document_loaders/parsers/language).
    - Follow the example set by **`test_cpp.py`** to establish fundamental tests for the parsed elements in the new language.
4. **Integration into the Parser and Text Splitter**:
    - Incorporate your new language within the **`language_parser.py`** file. Ensure to update LANGUAGE_EXTENSIONS and LANGUAGE_SEGMENTERS along with the docstring for LanguageParser to recognize and handle the added language.
    - Also, confirm that your language is included in **`text_splitter.py`** in class Language for proper parsing.

By following these steps and ensuring comprehensive testing and integration, you'll successfully extend language support using the Tree-Sitter template.

Best of luck!


## Related

- Document loader [conceptual guide](/docs/concepts/#document-loaders)
- Document loader [how-to guides](/docs/how_to/#document-loaders)