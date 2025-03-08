---
date: 2025-02-26
type: fact
aliases:
  -
hubs:
  - "[[langgraph]]"
---

# LangGraph Core Components

With LangGraph, we're going to be implementing a well defined control flow or a state machine or a graph, whatever you want to call it.

But the idea here is that whithin this well defined and well scoped control flow, we're going to be leveraging LLMs and they're going to have a very crucial role.

And to get started, we need three LangGraph core components:
- nodes
- edges
- conditional edges
- state


## Nodes

Nodes are literally Python functions, so you can put here any code you want. The code can be deterministic code (regular code) or it can be Python code that calls an LLM, or it can even be an LLM agent. You have full flexibility of what you want to do inside a node.

### START and END nodes

These two nodes don't really do anything, they're just there to help you define the beginning and the end of the graph's execution. So you can think about them as no operation.

### Input and output

Although we just said that nodes are just regular Python functions, their input and output are a bit special. The input of nodes is the state of the graph, and the output of nodes is always a dictionary with the keys of what do we want to update in our state.

And the result of this is that every node is going to update the state in the graph. The state object is going to change over time.

## Edges

Edges connect those nodes within the graph's execution


## Conditional Edges

Conditional edges are helped to make decisions whether to got to node A or to node B, and this is dynamic and can be very, very flexible.
```py
workflow.add_edge(RETRIEVE, GRADE_DOCUMENTS) # create an edge from node RETRIEVE to node GRADE_DOCUMENTS
workflow.add_conditional_edge( # create a conditional edge from node GRADE_DOCUMENTS, and the condition is decide_to_generate which decides whether to go to node WEBSEARCH or to node GENERATE
    GRADE_DOCUMENTS,
    decide_to_generate, # you can make a decision based on the state here
    {
        WEBSEARCH: WEBSEARCH,
        GENERATE: GENERATE,
    }
)
```
Edges can also access the state, so you can make decisions based on the state of the graph.


## State

The state is simply a Python dictionary that is going to have information that is important for us to keep track in the graph, maybe some nodes execution results, maybe some temporary results, or even our chat history. It can be fairly simple or it can be very complex, it's up to you.

### Scope of State
it's available for every node to access within our graph execution, and it's also available on each edge

### Local or Persistent State
It's local to graph that is. However, you can also persist it into persistent storage. So if you want to stop for some reason the flow execution of the graph. And we want to continue from that same point with that given state, we can do it with persistence.
