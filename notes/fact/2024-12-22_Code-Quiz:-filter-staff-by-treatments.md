---
date: 2024-12-22
type: fact
aliases:
  -
hubs:
  - "[[Tanstack]]"
---

# Code Quiz: filter staff by treatments


```ts
// src/components/staff/hooks/useStaff.ts 

import type { Staff } from "@shared/types";

import { axiosInstance } from "@/axiosInstance";
import { queryKeys } from "@/react-query/constants";
import { useQuery } from "@tanstack/react-query";
import { useCallback, useState } from "react";
import { filterByTreatment } from "../utils";

async function getStaff(): Promise<Staff[]> {
  const { data } = await axiosInstance.get('/staff');
  return data;
}

export function useStaff() {
  const [filter, setFilter] = useState('all');  // track the filter coefficient

  // create and memoize the select function
  const selectFn = useCallback((staff: Staff[], filter: string) => {
    if (filter === 'all') return staff  // when filter is `all`, return all the staff
    return filterByTreatment(staff, filter);
    //     ^^^^^^^^^^^^^^^^^ this is pre-written by lecturer, we just use it to filter the staff by treatment, but not included `all`
  }, [filter]);
  //  ^^^^^^ useCallback dependencies, only if `filter` changes, this fn will be re-created

  const fallback: Staff[] = [];

  const { data: staff } = useQuery({
    queryKey: [queryKeys.staff],
    queryFn: getStaff,
    placeholderData: fallback,
    select: (data) => selectFn(data, filter), // because it only pass queryFn result to here, so we need to use anonymous function to pass the `filter` value to `selectFn`
  })


  return { staff, filter, setFilter };
  //              ^^^^^^^^^^^^^^^^^ provide the filter state and setter to component, so that user can change the filter value
}

```

![staff-filter.png](../assets/imgs/staff-filter.png)

