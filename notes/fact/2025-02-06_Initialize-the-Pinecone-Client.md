---
date: 2025-02-06
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# Initialize the Pinecone Client

Following the steps in [[2025-02-06_Introducing-Pinecone|Introducing Pinecone]], we want to create a client and wrap it up with LangChain.

```sh
 tree app/chat
app/chat
├── chat.py
├── create_embeddings.py
├── embeddings # create embeddings module
│   ├── __init__.py # __init__.py to initialize the module
│   └── openai.py # write codes to provide embeddings model using openai model
├── __init__.py
├── models
│   └── __init__.py
├── score.py
└── vector_stores # create vector_stores module
    ├── __init__.py # __init__.py to initialize the module
    └── pinecone.py # write codes to provide vector store using pinecone
```

Provide embeddings model in module `app.chat.embeddings.openai`:

File: /home/matt/Projects/pdf/app/chat/embeddings/openai.py
```python
from langchain_openai import OpenAIEmbeddings

embeddings = OpenAIEmbeddings(model="text-embedding-3-large")

```

Provide vector store in module `app.chat.vector_stores.pinecone`:

File: /home/matt/Projects/pdf/app/chat/vector_stores/pinecone.py
```python
import os
from langchain_pinecone import PineconeVectorStore
from pinecone import Pinecone
from app.chat.embeddings.openai import embeddings

pc = Pinecone(api_key=os.getenv("PINECONE_API_KEY"))
index_name = os.getenv("PINECONE_INDEX_NAME", 'docs')

index = pc.Index(index_name) # connect to the index with raw Pinecone client

# Wrap the Pinecone client with LangChain for better integration with the app
vector_store = PineconeVectorStore(
    index=index, 
    embedding=embeddings
)
```

Now we have a wrapped Pinecone client that can be used in the application. You can refer to [[2025-01-29_Building-a-Retrieval-Chain|the previous way of using ChromaDB]] to use the Pinecone client in the application.
