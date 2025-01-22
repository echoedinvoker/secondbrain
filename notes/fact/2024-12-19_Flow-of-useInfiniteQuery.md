---
date: 2024-12-19
type: fact
aliases:
  -
hubs:
  - "[[Tanstack]]"
---

# Flow of useInfiniteQuery

After [[2024-12-19_Intro-of-useInfiniteQuery|intro of useinfiniteQuery]], we want to further understand the flow of useInfiniteQuery below:

![flow1.png](../assets/imgs/flow1.png)

![flow2.png](../assets/imgs/flow2.png)

![flow3.png](../assets/imgs/flow3.png)

![flow4.png](../assets/imgs/flow4.png)

Assuming that there are only 2 pages of data, so getNextPageParam will return undefined after the 2nd page.

![flow5.png](../assets/imgs/flow5.png)
