---
date: 2025-03-03
type: fact
aliases:
  -
hubs:
  - "[[langgraph]]"
---

# Logic of node grade_documents

The logic of this node is to use [[2025-03-03_retrieva_grader-chain|the chain]] we previously made to check if each document in the state is related to the question in the state one by one. If there are any unrelated documents, they will be removed from the state, and the web_search of the state will be set to true.

```sh
 tree
.
├── graph
│   ├── chains
│   │   ├── __init__.py
│   │   ├── retrieval_grader.py
│   │   └── tests
│   │       ├── __init__.py
│   │       └── test_chains.py
│   ├── consts.py
│   ├── graph.py
│   ├── __init__.py
│   ├── nodes
│   │   ├── grade_documents.py # new file, write the logic(function) of node `grade_documents`
│   │   ├── __init__.py
│   │   └── retrieve.py
│   └── state.py
├── ingestion.py
└── main.py

```

```py
from typing import Any, Dict
from graph.chains.retrieval_grader import retrieval_grader
from graph.state import GraphState
from graph.chains.retrieval_grader import GradeDocument


# logic of node grade_documents
def grad_documents(state: GraphState) -> Dict[str, Any]:
    """
    Determines whether the retrieved documents are relevant to the question.
    If any document is not relevant, we will set a flag to run web search.

    Args:
        state (dict): The current graph state

    Returns:
        state (dict): Filtered out irrelevant documents and updated web_search state
    """

    print("---CHECK DOCUMENT RELEVANCE TO QUESTION---")
    question = state["question"]
    documents = state["documents"]

    filtered_docs = []
    web_search = False

    for doc in documents:
        res = retrieval_grader.invoke(
                {"question": question, "document": doc.page_content}
        )

        if not isinstance(res, GradeDocument):
            raise ValueError(f"Unexpected response from retrieval grader: {res}")

        if res.binary_score == "yes":
            filtered_docs.append(doc)
        else:
            web_search = True

    return {"documents": filtered_docs, "web_search": web_search}
            
```
