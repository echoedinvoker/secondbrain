---
date: 2024-10-15
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Help! My next page is rendering with out of date data!

When a route is determined to be static, it may always render old data. There are several ways to control caching:

| Method | Description |
|--------|-------------|
| Time-Based | Every X seconds, ignore the cached response and fetch new data |
| On-Demand | Forcibly purge a cached response |
| Disable Caching | Don't do any caching at all |

[[2024-10-15_Cache-control---Disable-Caching|Disable Caching]] is the last option to consider, as it requires the server to recalculate the response every time, which will increase the server's workload.

[[2024-10-15_Cache-control---Time-Based|Time-Based]] and [[2024-10-15_Cache-control---On-Demand|On-Demand]] are suitable for their respective different scenarios.
