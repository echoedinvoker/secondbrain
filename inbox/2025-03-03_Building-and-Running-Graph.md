---
date: 2025-03-03
type: fact
aliases:
  -
hubs:
  - "[[langgraph]]"
---

# Building and Running Graph

In this topic, we need to create nodes and edges and combine them to form a graph.


## Better Import Way

```sh
 tree
.
├── graph
│   ├── chains
│   │   ├── generation.py
│   │   ├── __init__.py
│   │   ├── retrieval_grader.py
│   │   └── tests
│   │       ├── __init__.py
│   │       └── test_chains.py
│   ├── consts.py
│   ├── graph.py
│   ├── __init__.py
│   ├── nodes
│   │   ├── generate.py
│   │   ├── grade_documents.py
│   │   ├── __init__.py  # add some codes here for better import way from other modules
│   │   ├── retrieve.py
│   │   └── web_search.py
│   └── state.py
├── ingestion.py
├── main.py
├── Pipfile
└── Pipfile.lock
```

```py
from graph.nodes.generate import generate
from graph.nodes.grade_documents import grad_documents
from graph.nodes.retrieve import retrieve
from graph.nodes.web_search import websearch


__all__ = ['generate', 'grad_documents', 'retrieve', 'websearch']

```

Now, other modules can import the nodes' functions like this:

```py
from graph.nodes import generate, grad_documents, retrieve, websearch
```

Which is more readable and maintainable.


## Write keys of nodes

```sh
 tree
.
├── graph
│   ├── chains
│   ├── consts.py # write keys of nodes here
│   ├── graph.py
│   ├── __init__.py
│   ├── nodes
│   └── state.py
├── ingestion.py
├── main.py
├── Pipfile
└── Pipfile.lock
```
```py
RETRIEVE = 'retrieve'
GRADE_DOCUMENTS = 'grade_documents'
GENERATE = 'generate'
WEBSEARCH = 'websearch'
```

This is a good method to avoid typing errors when reusing strings, in this case, the keys of nodes.


## Create a Graph

```sh
 tree
.
├── graph
│   ├── chains
│   ├── consts.py
│   ├── graph.py # create a graph here
│   ├── __init__.py
│   ├── nodes
│   └── state.py
├── ingestion.py
├── main.py
├── Pipfile
└── Pipfile.lock
```
```py
from dotenv import load_dotenv
from langgraph.graph import END, StateGraph
from graph.consts import RETRIEVE, GRADE_DOCUMENTS, GENERATE, WEBSEARCH
from graph.nodes import generate, grad_documents, retrieve, websearch
from graph.state import GraphState


load_dotenv()

workflow = StateGraph(GraphState)

workflow.add_node(RETRIEVE, retrieve)
workflow.add_node(GRADE_DOCUMENTS, grad_documents)
workflow.add_node(WEBSEARCH, websearch)
workflow.add_node(GENERATE, generate)

workflow.set_entry_point(RETRIEVE)
workflow.add_edge(RETRIEVE, GRADE_DOCUMENTS)

```

But there are two paths behind the node `grade_documents`, so we need to use `.add_conditional_edge` to create conditional edges and make a function as its judgment logic.

![cond-edge-from-grade-doc.png](../assets/imgs/cond-edge-from-grade-doc.png)

```py
...

workflow.add_edge(RETRIEVE, GRADE_DOCUMENTS)

# write a function to decide which path to go
def decide_to_generate(state: GraphState) -> str:
    print("---ASSESS GRADED DOCUMENTS---")

    if state["web_search"]:
        print(
            "---DECISION: NOT ALL DOCUMENTS ARE NOT RELEVANT TO QUESTION---"
        )
        return WEBSEARCH

    print("---DECISION: ALL DOCUMENTS ARE RELEVANT TO QUESTION---")
    return GENERATE

# add conditional edges with above function
workflow.add_conditional_edges(
    GRADE_DOCUMENTS,
    decide_to_generate,
    # there is the third parameter that can map the return from `decide_to_generate` to other nodes
    # so if we write `WEB_SEARCH: END`, the return value `WEB_SEARCH` will result to go to the node `END` instead of `WEB_SEARCH`
    # but in this case, we don't need to map the return value to other nodes, just a demonstration purpose
    {
        WEBSEARCH: WEBSEARCH,
        GENERATE: GENERATE,
    }
)
workflow.add_edge(WEBSEARCH, GENERATE)
workflow.add_edge(GENERATE, END)

graph = workflow.compile()

print(graph.get_graph().draw_mermaid())

```

Run the codes above, get the mermaid codes and paste them to the [Mermaid Live Editor](https://mermaid.live) to see the graph.

![mermaid-graph-for-reflect-docs.png](../assets/imgs/mermaid-graph-for-reflect-docs.png)
