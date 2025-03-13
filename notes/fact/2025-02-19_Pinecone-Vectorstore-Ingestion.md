---
date: 2025-02-19
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# Pinecone Vectorstore Ingestion

```py
import os
from dotenv import load_dotenv
from langchain.text_splitter import RecursiveCharacterTextSplitter
from langchain_community.document_loaders import ReadTheDocsLoader
from langchain_openai import OpenAIEmbeddings
from langchain_pinecone import PineconeVectorStore

load_dotenv()


embeddings = OpenAIEmbeddings(model="text-embedding-3-small")

def ingest_docs():
    loader = ReadTheDocsLoader("langchain-docs/api.python.langchain.com/en/latest/")
    raw_documents = loader.load()
    print(f"Loaded {len(raw_documents)} documents")

    text_splitter = RecursiveCharacterTextSplitter(chunk_size=20000, chunk_overlap=1000)
    documents = text_splitter.split_documents(raw_documents)

    for doc in documents:
        new_url = doc.metadata["source"]
        new_url = new_url.replace("langchain-docs", "https:/")
        doc.metadata.update({"source": new_url})

    print(f"Going to add {len(documents)} documents to Pinecone")
    PineconeVectorStore.from_documents(
        documents,
        embeddings,
        index_name=os.environ["INDEX_NAME"],
    )

    print("****Loading to vector store is complete****")


if __name__ == "__main__":
    ingest_docs()

```

## ReadTheDocs & ReadTheDocsLoader

`ReadTheDocs` is a library for writing and hosting documentation for GitHub projects. Many popular projects use it to build and host their documentation. The Langchain documentation is built with it as well. So, in this script, we are loading the Langchain documentation with loader `ReadTheDocsLoader`.


## Splitting Documents

In the video, he use gpt-3-5-turbo model which token limit is 4096. Assume that we have a concise output which takes about 2000 tokens. Better split the rest of tokens to five or four chunks. So, he use `chunk_size=600` and `chunk_overlap=50`.

We never want to reduce the size of the chunks because it'll lose the context of the document. 

But now we use gpt-4o-mini which token limit is 128000. So, we can increase the chunk size to 20000 and chunk overlap to 1000. (I guess...)


## Running the Script and checking on Pinecone

```sh
 python ingestion.py
Loaded 344 documents
Going to add 422 documents to Pinecone
****Loading to vector store is complete****
```

![check-pinecone-for-docs.png](assets/imgs/check-pinecone-for-docs.png)

Special attention should be paid to ensuring that the quantity of 'Record count' matches the quantity of console log.

In the code, we have rewritten the source of metadata, so we also need to check if the source of each embedding is correct.





```sh
