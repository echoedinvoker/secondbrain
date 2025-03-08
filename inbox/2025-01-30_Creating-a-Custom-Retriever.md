---
date: 2025-01-30
type: fact
aliases:
  -
hubs:
  - "[[langchain]]"
---

# Creating a Custom Retriever

Create a custom retriever in the course video to avoid retrieving duplicate documents.

```python
# ./redundant_filter_retriever.py

from langchain.embeddings.base import Embeddings
from langchain.vectorstores import Chroma
from langchain.schema import BaseRetriever

class RedundantFilterRetriever(BaseRetriever):
#                              ^^^^^^^^^^^^^ abstract class that forces you to implement the following methods
#                                            1. get_relevant_documents  2. aget_relevant_documents (for async, we won't use it here)
    embeddings = Embeddings
    chroma = Chroma

    def get_relevant_documents(self, query):
        emb = self.embeddings.embed_query(query)

        return self.chroma.max_marginal_relevance_search_by_vector(
        #                 ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ Chroma provides this method to remove redundant documents
            embedding=emb,
            lambda_mult=0.8 # the value means how much you can tolerate redundancy
                            # the higher the value, the more redundancy you can tolerate,
                            # which means retrieved documents are more similar to each other
                            # the lower the value, the less redundancy you can tolerate,
                            # which means retrieved documents are more diverse
        )

    # although we don't use it here, we need to implement it
    async def aget_relevant_documents(self):
        return []
```

But in newer versions of Langchain, the retriever has included the new search type `mmr`, we can use it to remove redundant documents instead of creating custom retriever. Check [[2025-01-30_Retrieve-with-MMR-search-type|Retrieve with MMR search type]] for more information.
