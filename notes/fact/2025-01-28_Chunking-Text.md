---
date: 2025-01-28
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# Chunking Text

In [[2025-01-28_Loading-Files-with-Document-Loaders|Loading Files with Document Loaders]], we learned how to load files as Document objects with loader classes. Now, we'll learn how to split text into multiple Document objects, which means splitting a large text into smaller parts.

```py
from langchain_community.document_loaders import TextLoader
from langchain_text_splitters import RecursiveCharacterTextSplitter # import the splitter class

loader = TextLoader("facts.txt")

# Create an instance of the splitter class
text_splitter_recursive = RecursiveCharacterTextSplitter(
    # this type of splitter's default seperator is ["\n\n", "\n", " ", ""],
    # which means it first keep paragraphs, then sentences, and finally words
    # we can also specify the seperator we want to use especially for other languages
    chunk_size=100, # if the chunk size is 100, splitter will start to find the seperator backward from the 100th character
    chunk_overlap=20 # to increase the continuity of the chunks
)

documents = loader.load_and_split(text_splitter_recursive)
#                  ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
#                  replace .load() with .load_and_split() and pass the splitter instance as an argument

print(documents)
```

Let's see the output of the above code:

```sh 
$ python main.py

[
  Document(
    metadata={'source': 'facts.txt'},
    page_content='1. "Dreamt" is the only English word that ends with the letters "mt."'
  ),
  Document(
    metadata={'source': 'facts.txt'},
    page_content="2. An ostrich's eye is bigger than its brain."
  ),
  Document(
    metadata={'source': 'facts.txt'},
    page_content='3. Honey is the only natural food that is made without destroying any kind of life.'
  ),
  Document(
    metadata={'source': 'facts.txt'},
    page_content='4. A snail can sleep for three years.'
  ),
  Document(
    metadata={'source': 'facts.txt'},
    page_content="5. The longest word in the English language is 'pneumonoultramicroscopicsilicovolcanoconiosis.'"
  ),
  Document(
    metadata={'source': 'facts.txt'},
    page_content="6. The elephant is the only mammal that can't jump."
  ),
  Document(
    metadata={'source': 'facts.txt'},
    page_content="7. The letter 'Q' is the only letter not appearing in any U.S. state name."
  )

  # ... and so on

]
```


## Alternative way

Instead of `loader.load_and_split()`, we can also user the `splitter.split_documents()` method to split the text into multiple documents.

```py
from langchain_community.document_loaders import TextLoader
from langchain_text_splitters import RecursiveCharacterTextSplitter

loader = TextLoader("facts.txt")
text_splitter_recursive = RecursiveCharacterTextSplitter(
    chunk_size=100,
    chunk_overlap=20
)
documents = loader.load()

result = text_splitter_recursive.split_documents(documents)
#                               ^^^^^^^^^^^^^^^^^^^^^^^^^^^

print(result) # same output as above
```


