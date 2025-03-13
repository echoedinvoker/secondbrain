---
date: 2025-02-17
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# Why Use Recursive CTEs?

We use Instagram's recommendation system to explain why to use recursive CTE.

![instegram-suggestions.png](../assets/imgs/instegram-suggestions.png)

The recommended list above is generated from accounts I am already following, based on the accounts that those accounts are following, and recommended to me.

![diagram-suggest-follow-2-layer.png](../assets/imgs/diagram-suggest-follow-2-layer.png)

We can use `JOIN` clause to get the suggested accounts in a single layer. But, if we want to get the suggested accounts in multiple layers, it'll become very complex to use `JOIN` clause. This is where recursive CTEs come in handy.

![multi-layer-suggestions.png](../assets/imgs/multi-layer-suggestions.png)
The above is a tree structure that is very suitable for using recursive CTEs to handle. But actually, Kevin Hart may also be following me, as shown in the following picture.

![reverse-follow-me.png](../assets/imgs/reverse-follow-me.png)

So actually the suggestion system should be a Graph structure, but for the sake of simplicity, we will assume that there is no reverse follow situation. We hope that this structure is a tree structure, so that learning in the future will be easier to understand.




