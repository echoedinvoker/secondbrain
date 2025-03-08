---
date: 2025-03-02
type: fact
aliases:
  -
hubs:
  - "[[langgraph]]"
---

# Ingestion


## What we will do in this topic?

We will load documents from multiple websites and index them in the vector storage `Chroma`.


## Discaimer

Becase this section focuses on the retrieval of documents, not ingestion, so we will only use default settings for the ingestion process, there is lots of optimization for the ingestion process, but we will not cover it in this section.


## Implement...

### load and split documents

```python
from dotenv import load_dotenv
from langchain.text_splitter import RecursiveCharacterTextSplitter
from langchain_community.document_loaders import WebBaseLoader


load_dotenv()


urls = [
    "https://lilianweng.github.io/posts/2023-06-23-agent/",
    "https://lilianweng.github.io/posts/2023-03-15-prompt-engineering/",
    "https://lilianweng.github.io/posts/2023-10-25-adv-attack-llm/",
]

docs = [WebBaseLoader(url).load() for url in urls]

# [
#     [Document(...)],
#     [Document(...)],
#     [Document(...)]
# ]
# this is a nested list, we need to flatten it
docs_list = [item for sublist in docs for item in sublist]

# [
#     Document(...),
#     Document(...),
#     Document(...)
# ]

# then we split each document into chunks
text_splitter = RecursiveCharacterTextSplitter.from_tiktoken_encoder(
    chunk_size=250, chunk_overlap=0
)
doc_splits = text_splitter.split_documents(docs_list)

# [
#     Document(),
#     Document(),
#     Document(),
#     Document(),
#     Document(),
#     Document(),
#     Document(),
#     Document(),
#     Document(),
#     Document(),
#     Document(),
#     Document(),
#     Document(),
#     Document(),
#     Document(),
#
#     ...
#     ...
#     ...
#
# ]
```

### index documents and persist them

```python 
from dotenv import load_dotenv
from langchain.text_splitter import RecursiveCharacterTextSplitter
from langchain_community.document_loaders import WebBaseLoader
from langchain_chroma import Chroma # import Chroma
from langchain_openai import OpenAIEmbeddings # import OpenAIEmbeddings


load_dotenv()


urls = [
    "https://lilianweng.github.io/posts/2023-06-23-agent/",
    "https://lilianweng.github.io/posts/2023-03-15-prompt-engineering/",
    "https://lilianweng.github.io/posts/2023-10-25-adv-attack-llm/",
]

docs = [WebBaseLoader(url).load() for url in urls]
docs_list = [item for sublist in docs for item in sublist]
text_splitter = RecursiveCharacterTextSplitter.from_tiktoken_encoder(
    chunk_size=250, chunk_overlap=0
)
doc_splits = text_splitter.split_documents(docs_list)

# create vector storage from documents we got above, it'll index the documents and persist them directly by this method `.from_documents`
vectorstore = Chroma.from_documents(
    documents=doc_splits,
    collection_name="rag-chroma",
    embedding=OpenAIEmbeddings(), # use just use default embedding model, it might be `text-embedding-3-small`
    persist_directory="./.chroma", # persist the indexed documents in the local sqlite database in the `.chroma` directory
)

```

Let's run the above code to persist the indexed documents in the local sqlite database in the `.chroma` directory.

```sh
python ingestion.py

 tree -a
.
├── .chroma # this is the directory where the indexed documents are stored
│   ├── ae2b3f13-ed91-4e30-a86d-7ad7097f9d03
│   │   ├── data_level0.bin
│   │   ├── header.bin
│   │   ├── length.bin
│   │   └── link_lists.bin
│   └── chroma.sqlite3
├── .env
├── graph
├── ingestion.py
├── main.py
└── main.py
```

Then, we comment out the code that we used to index the documents, and create a retriever object from it to provide the similarity search for our application.

```py
from dotenv import load_dotenv
from langchain.text_splitter import RecursiveCharacterTextSplitter
from langchain_community.document_loaders import WebBaseLoader
from langchain_chroma import Chroma
from langchain_openai import OpenAIEmbeddings


load_dotenv()


urls = [
    "https://lilianweng.github.io/posts/2023-06-23-agent/",
    "https://lilianweng.github.io/posts/2023-03-15-prompt-engineering/",
    "https://lilianweng.github.io/posts/2023-10-25-adv-attack-llm/",
]

docs = [WebBaseLoader(url).load() for url in urls]
docs_list = [item for sublist in docs for item in sublist]
text_splitter = RecursiveCharacterTextSplitter.from_tiktoken_encoder(
    chunk_size=250, chunk_overlap=0
)
doc_splits = text_splitter.split_documents(docs_list)

# vectorstore = Chroma.from_documents(  # comment out the indexing process
#     documents=doc_splits,
#     collection_name="rag-chroma",
#     embedding=OpenAIEmbeddings(),
#     persist_directory="./.chroma",
# )

# create a retriever object from the indexed documents to provide the similarity search for our application
retriever = Chroma(
    collection_name="rag-chroma",
    persist_directory="./.chroma",
    embedding_function=OpenAIEmbeddings(),
)

```
