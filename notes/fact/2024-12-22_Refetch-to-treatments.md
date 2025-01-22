---
date: 2024-12-22
type: fact
aliases:
  -
hubs:
  - "[[Tanstack]]"
---

# Refetch to treatments

In [[2024-12-22_Intro-of-refetch|this topic]], we have said that treatments data is suitable for suppressing re-fetching. So, we want to implement it now.

Refetch mainly occurs depending on 1. data stale 2. trigger event. So we need to extend the stale time (default 0) and disable some trigger events.

```ts
// src/components/treatments/hooks/useTreatments.ts

import type { Treatment } from "@shared/types";

import { axiosInstance } from "@/axiosInstance";
import { queryKeys } from "@/react-query/constants";
import { useQuery, useQueryClient } from "@tanstack/react-query";

async function getTreatments(): Promise<Treatment[]> {
  const { data } = await axiosInstance.get('/treatments');
  return data;
}

export function useTreatments(): Treatment[] {
  const fallback: Treatment[] = []
  const { data = fallback } = useQuery({
    queryKey: [queryKeys.treatments],
    queryFn: getTreatments,
    // treatments data doesn't change frequently, and it's no matter if it's a little stale
    // so we can suppress re-fetch mechanism because default re-fetching is aggressive and resource-consuming
    staleTime: 1000 * 60 * 10, // 10 minutes
    gcTime: 1000 * 60 * 15, // 15 minutes(this must be longer than staleTime, or it makes no sense)
    refetchOnMount: false, // we don't want to refetch on component mount because we are using prefetching
    refetchOnWindowFocus: false,
    refetchOnReconnect: false,
  })

  return data;
}

export function usePrefetchTreatments() {
  const queryClient = useQueryClient()
  queryClient.prefetchQuery({
    queryKey: [queryKeys.treatments],
    queryFn: getTreatments,
  })
}

```

![refocus-no-change.png](../assets/imgs/refocus-no-change.png)

![change-to-homepage.png](../assets/imgs/change-to-homepage.png)

So, we can find that the refeching settings on `useQuery` don't work on `client.prefetchQuery`. So, we need to set the same settings on `prefetchQuery`.


```ts
// src/components/treatments/hooks/useTreatments.ts

import type { Treatment } from "@shared/types";

import { axiosInstance } from "@/axiosInstance";
import { queryKeys } from "@/react-query/constants";
import { useQuery, useQueryClient } from "@tanstack/react-query";

async function getTreatments(): Promise<Treatment[]> {
  const { data } = await axiosInstance.get('/treatments');
  return data;
}

export function useTreatments(): Treatment[] {
  const fallback: Treatment[] = []
  const { data = fallback } = useQuery({
    queryKey: [queryKeys.treatments],
    queryFn: getTreatments,
    staleTime: 1000 * 60 * 10,
    gcTime: 1000 * 60 * 15,
    refetchOnMount: false,
    refetchOnWindowFocus: false,
    refetchOnReconnect: false,
  })

  return data;
}

export function usePrefetchTreatments() {
  const queryClient = useQueryClient()
  queryClient.prefetchQuery({
    queryKey: [queryKeys.treatments],
    queryFn: getTreatments,
    // copy the same staleTime and gcTime settings to prefetchQuery
    staleTime: 1000 * 60 * 10,
    gcTime: 1000 * 60 * 15,
    // because prefetchQuery is imperatively called, it has no trigger event related settings
  })
}
```
