---
date: 2025-03-06
type: fact
aliases:
  -
hubs:
  - "[[langgraph]]"
---

# Conditional Branching With Async Execution

![conditional-branching.png](../assets/imgs/conditional-branching.png)

For this topic, we need to implement the graph above. "A" can fan out to "B", "C", or "C", "D", so it is a conditional edge and a fan out.

```py
from dotenv import load_dotenv
load_dotenv()

import operator
from typing import Annotated, Any, Sequence
from typing_extensions import TypedDict
from langgraph.graph import StateGraph, END


class State(TypedDict):
    aggregate: Annotated[list[str], operator.add]
    which: str # bc or cd, to determine which path to take, its value will be passed when invoking the graph


class ReturnNodeValue:
    def __init__(self, node_secret: str):
        self._value = node_secret

    def __call__(self, state: State) -> Any:
        print(f"Adding {self._value} to {state['aggregate']}")
        return {"aggregate": [self._value]}

builder = StateGraph(State)

builder.add_node("A", ReturnNodeValue("I am A"))
builder.add_node("B", ReturnNodeValue("I am B"))
builder.add_node("C", ReturnNodeValue("I am C"))
builder.add_node("D", ReturnNodeValue("I am D"))
builder.add_node("E", ReturnNodeValue("I am E")) # add one more node 

builder.set_entry_point("A")

# logic to determine which path to take by state `which`
def route_bc_or_cd(state: State) -> Sequence[str]:
#                                   ^^^^^^^^^^^^^ instead of str, it should be Sequence[str] because it's a fan out
    if state["which"] == "bc":
        return ["B", "C"]
    return ["C", "D"]

builder.add_conditional_edges(
    "A",
    route_bc_or_cd,
    ["B", "C", "D"] # the third argument is important, or the graph will connect "A" to all nodes when it's a fan out logic
)
builder.add_edge(["B", "C", "D"], "E") # fan in to "E"
builder.add_edge("E", END)

graph = builder.compile()

print(graph.get_graph().draw_mermaid())

```
![conditional-branch-mermaid.png](../assets/imgs/conditional-branch-mermaid.png)


