---
date: 2025-03-05
type: fact
aliases:
  -
hubs:
  - "[[langgraph]]"
---

# Persistence in LangGraph

In LangGraph, persistence means saving the state in persistent storage after executing any node, and being able to retrieve this state after multiple executions or interruptions.

It's important. Because first of all, it facilitates human in the loop workflows. So with it, we can create a node that is going to be dependent on the user input because LangGraph offers us is a very convenient way to **stop the execution** of our graph with the persistence mechanism.

**Memory** and persistence is super important because it also enables us for debugging and for history. And if we want even to have multiple sessions of a user, this is required.

LangGraph is doing this with **Checkpointer** objects. This is a persistence layer that LangGraph impelements for us. Basically what it'll do is save the state in persistent storage after executing any node.

```py
from langgraph.checkpoint.sqlte import SqliteSaver

memory = SqliteSaver.from_conn_string(":checkpoints.sqlite:")
graph = workflow.compile(checkpointer=memory)

```
Above is an example of how to use a Checkpointer object in LangGraph. We can use the `SqliteSaver` class to save the state in a SQLite database.

There are lots of other integrations that we can use with LangGraph to save the state in different persistent storage, remote, local, relational, non-relational, etc.
