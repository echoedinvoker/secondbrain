---
date: 2024-12-21
type: fact
aliases:
  -
hubs:
  - "[[Tanstack]]"
---

# Plan to prefetch treatments in the homepage

## Prefetching treatments

* We have already saw prefetch in chapter of pagination. [[2024-12-18_Prefetching|link]]
* Now we want to do prefetch by different trigger: prefetch treatments on home page load
    * user research: 85% of home page loads are followed by treatments tab loads
* Treatments don't change often, so cached data isn't really a problem
    - `queryKey` is simple --> Treatments data is stable instead of dynamic (suitable for prefetching)
* garbage collected if no `useQuery` is called after `gcTime`
    * if typically not loaded by default `gcTime` (5 minutes), we can consider to specify longer `gcTime`
* `prefetchQuery` is a method on the `queryClient`
    * adding to the client cache
    * `useQueryClient` returns `queryClient` (within Provider)
* Make a `usePrefetchTreatments` hook (custom) within `useTreatments.ts`
    * because uses the same query function and key as the `useTreatments` call
* call `usePrefetchTreatments` from Home component


## Visualizing flow

![prefetch-treatments.png](../assets/imgs/prefetch-treatments.png)

Prefetched data will immediately become stale by default, so regardless, useQuery will fetch new data. The difference is that in the case of prefetching, users can see something first.
