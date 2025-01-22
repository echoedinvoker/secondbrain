---
date: 2024-12-17
type: fact
aliases:
  -
hubs:
  - "[[Tanstack]]"
---

# Stale Data

![stale-data.png](../assets/imgs/stale-data.png)


**What is stale data?**
  * Data that is "expired" and is **ready** to be refetched (only ready, not yet refetched)
  * Still in the cache, stale data won't be removed until a new data is fetched

**Data refetch only triggers for stale data**
  * Trigger examples: component on mount, re-focusing.
  * When triggered, only **stale** data is refetched, not all data.

**How to tolerate data potentially being out of date?**

**Default stale time is 0**

![default0.png](../assets/imgs/default0.png)

It reduces the probability of users seeing expired data to a minimum.
