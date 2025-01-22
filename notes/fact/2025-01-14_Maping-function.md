---
date: 2025-01-14
type: fact
aliases:
  -
hubs:
  - "[[WASM]]"
---

# Maping function

In [[2025-01-10_Lost-case|this topic]], we implemented the lost case of our snake game but it looks a bit weird because the snake head color is gone when it hits the body.

![wheres-the-head.png](../assets/imgs/wheres-the-head.png)

In order to fix this, there are two ways to do it:

1. [[2025-01-14_Filter-the-overlapping-cell|Filter the overlapping cell]]

2. [[2025-01-14_Reverse-the-snake-cell-array|Reverse the snake cell array]]

No matter which way we choose, we should enable to see the snake head color when it hits the body.

![head-ontop-of-body.png](../assets/imgs/head-ontop-of-body.png)
