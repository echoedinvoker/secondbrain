---
date: 2024-12-22
type: fact
aliases:
  -
hubs:
  - "[[Tanstack]]"
---

# Filter with the select option

## Introduction

- Allow user to filter out any appointments that aren't available
- Why is the select option the best way to do this?
    - React Query memo-izes to reduce unnecessary computation
    - tech details:
        - triple equals comparison of select function
        - only runs if data changes or the function has changed (greatly reduces computation)
    - need a stable function (`useCallback` for anonymous function)
    - reference: https://tkdodo.eu/blog/react-query-data-transformations   
- State contained in hook (like `monthYear`)
- Filter function in utils: `getAvailableAppointments` (lecturer already created)


## Implementation

```ts
// src/components/appointments/hooks/useAppointments.ts

import dayjs from "dayjs";
import { useCallback, useEffect, useState } from "react";

import { AppointmentDateMap } from "../types";
import { getMonthYearDetails, getNewMonthYear } from "./monthYear";

import { useLoginData } from "@/auth/AuthContext";
import { axiosInstance } from "@/axiosInstance";
import { queryKeys } from "@/react-query/constants";
import { useQuery, useQueryClient } from "@tanstack/react-query";
import { getAvailableAppointments } from "../utils";

async function getAppointments(
  year: string,
  month: string
): Promise<AppointmentDateMap> {
  const { data } = await axiosInstance.get(`/appointments/${year}/${month}`);
  return data;
}

export function useAppointments() {
  const currentMonthYear = getMonthYearDetails(dayjs());
  const [monthYear, setMonthYear] = useState(currentMonthYear);

  function updateMonthYear(monthIncrement: number): void {
    setMonthYear((prevData) => getNewMonthYear(prevData, monthIncrement));
  }

  const [showAll, setShowAll] = useState(false);  // track if user wants to see all appointments
  const { userId } = useLoginData();  // get user id for filtering appointments

  // create a memoized function to filter appointments, memorization is important to reduce unnecessary computation
  const selectFn = useCallback((data: AppointmentDateMap, showAll: boolean) => {
  //               ^^^^^^^^^^^ to memoize the function, or this fn will be recreated on every times this hook is called
    if (showAll) return data;
    return getAvailableAppointments(data, userId);
    //     ^^^^^^^^^^^^^^^^^^^^^^^^ lecturer already created the logic of filtering appointments, we just use it to filter the appointments
  }, [userId]);
  //  ^^^^^^ useCallback dependencies, only recreate the function if userId changes

  const fallback: AppointmentDateMap = {};

  const { data: appointments = fallback } = useQuery({
    queryKey: [queryKeys.appointments, monthYear.year, monthYear.month],
    queryFn: () => getAppointments(monthYear.year, monthYear.month),
    select: (data) => selectFn(data, showAll),  // queryFn result will be passed to select function
                                                // and select function result will be stored in `data` property
                                                  
  })

  const queryClient = useQueryClient();

  useEffect(() => {
    const nextMonthYear = getNewMonthYear(monthYear, 1);
    queryClient.prefetchQuery({
      queryKey: [queryKeys.appointments, nextMonthYear.year, nextMonthYear.month],
      queryFn: () => getAppointments(nextMonthYear.year, nextMonthYear.month)
      // in prefetching, we don't need to add select function, because select function is only used to filter the data, it don't effect the cache
      // and prefetching is only to cache the data, it don't effect the `data` property of the useQuery hook
    })


  }, [monthYear, queryClient]);

  return { appointments, monthYear, updateMonthYear, showAll, setShowAll };
}

```
