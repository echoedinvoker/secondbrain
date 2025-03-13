---
date: 2025-02-08
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# Scope document retrieval

`db.as_retriever()` can be passed a `search_kwargs` parameter to filter the documents to be retrieved.

A simple example is shown below:

```py
retriever = vector_store.as_retriever(
    search_kwargs={
        "filter": {"source": "tweet"}  # only retrieve documents with source "tweet"
    }
)
```

More complex example is shown below:

```py
retriever = vector_store.as_retriever(
    search_kwargs={
        "filter": {
            "$and": [ # logical AND operation for multiple filters
                {"id": {"$gte": 1}},  # id >= 1
                {"name": {"$in": ["adam", "bob"]}}  # name in ["adam", "bob"]
            ]
        }
    }
)
```

