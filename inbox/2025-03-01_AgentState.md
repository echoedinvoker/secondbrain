---
date: 2025-03-01
type: fact
aliases:
  -
hubs:
  - "[[langgraph]]"
---

# AgentState

In order to use LangGraph to implement the functionality of Agent Executor, we need to define a custom state of the graph ourselves. This state will be used to track the states tracked in the original Agent Executor while loop.

In Agent Executor, it will continuously track the user input, agent outcome, and intermediate steps, so we add them as attributes to the custom state here.


File: /home/matt/Projects/langgraph-react/state.py
```python
import operator
from typing import Annotated, TypedDict, Union
from langchain_core.agents import AgentAction, AgentFinish


# define the custom state of the graph
class AgentState(TypedDict):
#                ^^^^^^^^^ by LangGraph documentation, custom state should inherit from TypedDict
    input: str # user input
    agent_outcome: Union[AgentAction, AgentFinish, None]
    #                                              ^^^^ this is for the state to the first node, there is no any agent outcome yet
    intermediate_steps: Annotated[list[tuple[AgentAction, str]], operator.add]
    #                   ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
    #                   When use `Annotated`, the first argument is the type of the attribute
    #                   The second argument the logic to update this attribute
    #                   Now only support `operator.add`, which means when node function return this key-value pair, it will be appended to the list of this attribute
```

Other attributes that are not annotated will be overridden when they have the same key-value pair in the node function return. If this key-value pair is not returned, it will remain unchanged.
