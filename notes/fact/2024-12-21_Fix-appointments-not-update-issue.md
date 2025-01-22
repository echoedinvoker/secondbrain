---
date: 2024-12-21
type: fact
aliases:
  -
hubs:
  - "[[Tanstack]]"
---

# Fix appointments not update issue

In [[2024-12-21_Start-to-write-useQuery-portion-of-useAppointments|this topic]], we found that the appointments data don't update when user switch to another month. Here we want to analyze the issue and find a solution.


## Why doesn't new data load?

* using the same key for every query
* after clicking arrow to load new month
    * data is stale (staleTime = 0), but...
        * nothing to trigger refetch, which means not one of the following events happened:
            * component remount
            * window refocus
            * running refetch function manually
            * automated refetch


## So, what's the solution?

So, do we need to create any trigger events above? No

A better approach is to have different keys for appointments for each month, so when the user switches months, the query for appointments for that month will be equivalent to the initial query, and data will be automatically re-fetched.


## Let's implement it!

```ts
// src/components/appointments/hooks/useAppointments.ts

import dayjs from "dayjs";
import { useState } from "react";

import { AppointmentDateMap } from "../types";
import { getAvailableAppointments } from "../utils";
import { getMonthYearDetails, getNewMonthYear } from "./monthYear";

import { useLoginData } from "@/auth/AuthContext";
import { axiosInstance } from "@/axiosInstance";
import { queryKeys } from "@/react-query/constants";
import { useQuery } from "@tanstack/react-query";

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
    //                                 ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ instead of monthYear, but monthYear.year and monthYear.month (be careful here!)
    queryFn: () => getAppointments(monthYear.year, monthYear.month),
    //                             ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ in fact, any dynamic value in queryFn should be dependent on queryKey
    //                                                             (if you install eslint-plugin-react-hooks, it will warn you about this)
  })

  return { appointments, monthYear, updateMonthYear, showAll, setShowAll };
}
```

## Test it!

![fix-appointments1.png](../assets/imgs/fix-appointments1.png)
Switching to next month...

![next-fix-app.png](../assets/imgs/next-fix-app.png)
