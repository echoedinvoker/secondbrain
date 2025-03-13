---
date: 2025-02-15
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# Medium Analyzer: Class Review: TextLoader, TextSplitter, OpenAIEmbeddings, Pinecone

As mentioned in [[2025-02-15_Medium-Analyzer:-Boilerplate-Project-Setup|the previous topic]], we want to implement a workflow: LOAD --> SPLIT --> EMBED --> STORE.  

Fortunately, Lanechain offers us multiple classes to complete this process with just a few lines of code. This topic will review the functions of these classes and how to use them.


## Document loaders

Although LLM requires text input, our data may come from different sources, such as CSV, JSON, SQL, or even WhatsApp messages. In order to make these data sources digestible by LLM, Langchain provides data loaders for various formats.

We can go to Lanechain's GitHub repository and check the `document_loaders` folder to see the available loaders.

![github-langchain-loaders.png](../assets/imgs/github-langchain-loaders.png)

Each loader has the same interface and do the similar job: taking the file, loading it, doing its formatting and returning as is. Basically it makes the data more digestible for LLMs.


## Text splitters

It allows us to take text which is large and split it into chunks.

The splitter has a lots of logic here because it has lots of splitting strategies we can use.

And chunk size is not trivial because it may change according to what we want to accomplish, what LLM model we are using and what embeddings system we are using.

```py
from lanechain_text_splitters import CharacterTextSplitter

text_splitter = CharacterTextSplitter(
    separator="\n\n",
    chunk_size=1000,
    chunk_overlap=200, # to keep context meaningful between chunks
    length_function=len, # which determines the length of the text by len()
                         # sometimes we may want to count the tokens instead of characters, then we need to use another function
    is_separator_regex=False
)
```

The above is an example of a splitter, where you can see that there are several parameters that can be adjusted, such as separator, chunk_size, chunk_overlap, length_function, and is_separator_regex.


## Embedding models

It's simply a black box that takes in inputs as text and returns vectors.

![take-text-return-vector.png](../assets/imgs/take-text-return-vector.png)

We'll use OpenAI embedding model `text-embedding-ada-002` because it's the most cheap and efficient one.

![text-embedding-ada-002.png](assets/imgs/text-embedding-ada-002.png)

Langchain provides text embedding models to create a uniform interface for us for access different embeddings from different providers.

So, switching between different embeddings is just a matter of changing the value of parameter in the code.


## Vector storage

It provides two functionalities:
1. search in the vector space to find the most similar vectors to a given vector
2. add new vectors to the vector space

We'll use Pinecone for vector storage and Langchain provides a class for it to better integrate into Langchain's ecosystem.


## Import above classes

```py
from dotenv import load_dotenv

## import above classes
from langchain_community.document_loaders import TextLoader
from langchain_text_splitters import CharacterTextSplitter
from langchain_openai import OpenAIEmbeddings
from langchain_pinecone import PineconeVectorStore

load_dotenv()

if __name__ == "__main__":
    print("Ingesting...")
    

