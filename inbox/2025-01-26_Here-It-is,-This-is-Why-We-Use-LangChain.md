---
date: 2025-01-26
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# Here It is, This is Why We Use LangChain

In [[2025-01-26_How-a-Typical-AI-Enabled-App-Works|this topic]], we discussed the steps involved in the working of an AI-enabled app. Langchain gives us automatic tools to automate every single one of these steps.


## Automating the file loading and parsing

Let's see an example:

When user uploads a document, we need to open the pdf file and parse the text because that a pdf file doesn't contain only text, it contains images, tables, and other stuff. So, the whole process is a bit complex. But with LangChain, we can do this in a couple of lines of code.

```python
from langchain.document_loader import UnstructuredPDFLoader
#                                     ^^^^^^^^^^^^^^^^^^^^^
# LangChain provides a class to load unstructured PDFs easily
loader = UnstructuredPDFLoader("earth.pdf")
data = loader.load()
print(data) # Document(page_content = "Earth is a planet ...")
```

There are also many other classes:
- UnstructuredHTMLLoader
- UnstructuredExcelLoader
- PyPDFLoader
- S3FileLoader
- GitHubIssuesLoader
- JSONLoader
- DiscordChatLoader
- GoogleDriveLoader



## Automating the vector database storage and querying

Let's jump to the step to store results in a vector database.

There are a ton of popular vector databases like PGVector, Pinecone, Chroma, Deep Lake, Weaviate, Redis and many more. 

We could spend a lot of time to figure out how each one works, and access it from our code. But with LangChain.

Or, we could just use some classes that are included in LangChain that completely wrap up the usage of these vector databases and makes using them incredibly easy.

Here is an example of how to use PGVector to store and search for documents:
```python
from langchain.vectorestores.pgvector import PGVector

db = PGVector.from_documents(
    embedding  = embeddings,
    documents = docs,
    collection_name = 'earth',
    connection_string = CONNECTION_STRING,
)
query = "Where does the word earth come from?"
docs = db.similarity_search(query)
```

Here is an example of how to use Pinecone to store and search for documents:
```py
from langchain.vectorestores.pinecone import Pinecone

db = Pinecone.from_documents(
    embedding  = embeddings,
    documents = docs,
    index_name = 'earth',
)

query = "Where does the word earth come from?"
docs = db.similarity_search(query)
```

You can see how easy it is to use these databases with LangChain. And even the vector databases are different, the code to use them is almost the same.



## Goal of LangChain

Provide _interchangeable_ tools to automate each step of a text generation pipeline.

- Has tools for loading, parsing, storing, querying, and passing data to models like ChatGPT

- Integrates with a variety of different services from various companies

- Relatively easy to swap out providers - don't want to use ChatGPT anymore? Swap in a different model in a few minutes
