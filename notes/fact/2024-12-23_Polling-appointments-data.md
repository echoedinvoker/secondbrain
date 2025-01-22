---
date: 2024-12-23
type: fact
aliases:
  -
hubs:
  - "[[Tanstack]]"
---

# Polling appointments data

In [[2024-12-23_Override-global-refetch-settings|this topic]], we overrode the global refetch settings for the appointments data, which make the query to the appointments data more aggressive.

However, even if the user does nothing, the appointments data in the server is likely to change frequently, which may mislead the user to decide based on outdated information.

So, we want to poll the appointments data every minute to keep the user informed about the latest appointments data.

We can use `refetchInterval` option to `useQuery` to achieve this. [docs](https://tanstack.com/query/latest/docs/react/examples/react/auto-refetching)

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

const commonSettings = {
  staleTime: 0,
  gcTime: 1000 * 60 * 5,
}

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

  const selectFn = useCallback((data: AppointmentDateMap, showAll: boolean) => {
    if (showAll) return data;
    return getAvailableAppointments(data, userId);
  }, [userId]);

  const fallback: AppointmentDateMap = {};

  const { data: appointments = fallback } = useQuery({
    queryKey: [queryKeys.appointments, monthYear.year, monthYear.month],
    queryFn: () => getAppointments(monthYear.year, monthYear.month),
    select: (data) => selectFn(data, showAll),
    refetchOnWindowFocus: true,
    refetchInterval: 1000 * 60 * 1,  // <---------- poll every minute
    ...commonSettings,
  })

  const queryClient = useQueryClient();

  useEffect(() => {
    const nextMonthYear = getNewMonthYear(monthYear, 1);
    queryClient.prefetchQuery({
      queryKey: [queryKeys.appointments, nextMonthYear.year, nextMonthYear.month],
      queryFn: () => getAppointments(nextMonthYear.year, nextMonthYear.month),
      ...commonSettings
    })
  }, [monthYear, queryClient]);

  return { appointments, monthYear, updateMonthYear, showAll, setShowAll };
}

```
