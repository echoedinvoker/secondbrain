---
date: 2024-12-22
type: fact
aliases:
  -
hubs:
  - "[[Tanstack]]"
---

# Code Quiz: prefetch appointments of next month


## Introduction

- This is complicated! So many moving parts
- Implement pre-fetch for the next month
    - use a method of the query client
    - get the query client using a React Query hook
- Implement pre-fetch in `useEffect`
    - dependency on `monthYear` (and `queryClient`)
    - better than putting pre-fetch in `updateMonthYear`
        - race conditions with async update of state
- Pay attention to pre-fetch args:
    - query key
    - arguments to `getAppointments`
        - const nextMonthYear = getNewMonthYear(monthYear, 1)


## Solution on my own

I decided to add `useEffect` in `useAppointments` and use `queryClient.prefetchQuery` to prefetch data for next month.

```ts
// src/components/appointments/hooks/useAppointments.ts

import dayjs from "dayjs";
import { useEffect, useState } from "react";

import { AppointmentDateMap } from "../types";
import { getMonthYearDetails, getNewMonthYear } from "./monthYear";

import { useLoginData } from "@/auth/AuthContext";
import { axiosInstance } from "@/axiosInstance";
import { queryKeys } from "@/react-query/constants";
import { useQuery, useQueryClient } from "@tanstack/react-query";

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

  const [showAll, setShowAll] = useState(false);
  const { userId } = useLoginData();

  const fallback: AppointmentDateMap = {};

  const { data: appointments = fallback } = useQuery({
    queryKey: [queryKeys.appointments, monthYear.year, monthYear.month],
    queryFn: () => getAppointments(monthYear.year, monthYear.month),
  })

  // Prefetch appointments of next month
  const queryClient = useQueryClient();  // Get the query client, its a hook so it can't be inside useEffect
  useEffect(() => { // instead of pre-fetching in updateMonthYear, do it in useEffect instead
    const nextMonthYear = getNewMonthYear(monthYear, 1); // even this is computed state, we can't put it out of useEffect because this is not functional components, so it won't be updated automatically
    queryClient.prefetchQuery({
      queryKey: [queryKeys.appointments, nextMonthYear.year, nextMonthYear.month],
      //                                 ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ use nextMonthYear here
      queryFn: () => getAppointments(nextMonthYear.year, nextMonthYear.month)
      //                             ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ use nextMonthYear here
    })
  }, [monthYear, queryClient]);
  //  ^^^^^^^^^^^^^^^^^^^^^^ remember not only monthYear but also queryClient as dependency

  return { appointments, monthYear, updateMonthYear, showAll, setShowAll };
}

```

![prefetch-next-month.png](../assets/imgs/prefetch-next-month.png)
