---
date: 2024-12-17
type: fact
aliases:
  -
hubs:
  - "[[Tanstack]]"
---

# StaleTime vs gcTime

There are three situations when we need to display data on the page:

 ![stale-vs-gc.png](../assets/imgs/stale-vs-gc.png)


**Fresh and in cache**

Display cached data and no re-fetching is needed.


**Stale and in cache**

Display cached data and re-fetching is needed.

Therefore, the old data will be displayed on the page first, and then updated to the new data.

And `isLoading` sticks to `false` in this situation. If you want to present some indication to the user that the fetch is in progress, you can use the `isFetching` flag instead of `isLoading`.


**Not in cache**

There are two situations that result in no data in the cache:

1. The data is needed for the first time after loading the app.

2. The data is cleared from the cache due to exceeding the gcTime.

In this situation, the page will wait until the fetch is complete before displaying the data, resulting in a blank page for a period of time or the option to display a loading screen.
