---
date: 2025-03-06
type: fact
aliases:
  -
hubs:
  - "[[langgraph]]"
---

# Async Nodes Benefits VS Challenges

## Drawbacks

When a graph has branches, these branches will be automatically processed in parallel, which can improve the performance of your graph, but it may also have some drawbacks.

1. State conflicts

When different branches of parallel processing modify same attributes in the state, these modifications may conflict with each other and lead to unexpected results.


2. Debugging

When a graph has branches, it may be difficult to debug the graph because the execution order of the branches is not guaranteed.


## Best practices

> When there are branches, avoid individual branches modifying the same attribute of the state. Ensure that each branch is isolated and does not affect the others.
