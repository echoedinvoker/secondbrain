---
date: 2024-12-21
type: fact
aliases:
  -
hubs:
  - "[[Tanstack]]"
---

# Start to write useQuery portion of useAppointments

Continuing from [[intro-to-appointments-system]], here we want to complete the useQuery portion of the useAppointments hook.

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

  // complete useQuery portion
  const fallback: AppointmentDateMap = {};
  const { data: appointments = fallback } = useQuery({
    queryKey: [queryKeys.appointments],  // here I deliberately missed some dependencies to make some error when we check the web app later
    queryFn: () => getAppointments(monthYear.year, monthYear.month),
  })

  return { appointments, monthYear, updateMonthYear, showAll, setShowAll };
}

```

![appointments.png](../assets/imgs/appointments.png)

But when we switch to the next month...

![appointments-next.png](../assets/imgs/appointments-next.png)


We will solve above issue in [[2024-12-21_Fix-appointments-not-update-issue|this topic]].
