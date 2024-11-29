---
date: 2024-11-11
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# Deciding when to use enums vs structs when there are many things need to define

![flowchart-to-decide-enum-vs-struct.png](../assets/imgs/flowchart-to-decide-enum-vs-struct.png)

Simply put, use enums when every thing requires the same methods, otherwise use structs.

Another consideration is that if a thing has a lot of properties, we will also choose to use structs because this will make the match syntax very long.
