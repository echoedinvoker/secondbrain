---
date: 2024-12-23
type: fact
aliases:
  -
hubs:
  - "[[Tanstack]]"
---

# Override global refetch settings

In [[2024-12-22_Global-refetch-settings|this topic]], we set a global refetch setting for all queries. It make all queries refetch not so much often.

It is fine for staff and treatments data, they are not changed so often, but for appointments data, it is better to refetch it more often. So we need to override the global refetch setting for appointments query.

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

    // override global refetch settings
    staleTime: 0,
    gcTime: 1000 * 60 * 5,
    refetchOnWindowFocus: true,
  })

  const queryClient = useQueryClient();

  useEffect(() => {
    const nextMonthYear = getNewMonthYear(monthYear, 1);
    queryClient.prefetchQuery({
      queryKey: [queryKeys.appointments, nextMonthYear.year, nextMonthYear.month],
      queryFn: () => getAppointments(nextMonthYear.year, nextMonthYear.month),

      // override global refetch settings,
      staleTime: 0,
      gcTime: 1000 * 60 * 5,
    })
  }, [monthYear, queryClient]);

  return { appointments, monthYear, updateMonthYear, showAll, setShowAll };
}

```

We found that `staleTime` and `gcTime` are duplicated in the `useQuery` and `prefetchQuery` functions. We can extract them to a constant and use it in both places.

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

// extract common settings out of the useQuery and prefetchQuery functions to here
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
    ...commonSettings,  // spread common settings here
  })

  const queryClient = useQueryClient();

  useEffect(() => {
    const nextMonthYear = getNewMonthYear(monthYear, 1);
    queryClient.prefetchQuery({
      queryKey: [queryKeys.appointments, nextMonthYear.year, nextMonthYear.month],
      queryFn: () => getAppointments(nextMonthYear.year, nextMonthYear.month),
      ...commonSettings  // spread common settings here
    })
  }, [monthYear, queryClient]);

  return { appointments, monthYear, updateMonthYear, showAll, setShowAll };
}

```
