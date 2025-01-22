---
date: 2024-12-21
type: fact
aliases:
  -
hubs:
  - "[[Tanstack]]"
---

# Intro to appointments system

The appointment system is the most complex feature in the Lazy Days project, so before we implement anything, we must first understand how the entire system works.

```bash
~/D/g/s/t/u/b/client > tree src/components/appointments/

src/components/appointments/
├── Appointment.tsx # page component
├── Calendar.tsx # component to list all DateBoxes of a month
├── DateBox.tsx # component to display appointments for a single day
├── hooks
│   ├── constants.ts
│   ├── monthYear.ts # functions to handle monthYear data, abstracted from useAppointments, so useAppointments can focus on the query
│   ├── useAppointments.ts # query for appointments, but also track monthYear and filter state
│   ├── useCancelAppointment.ts
│   └── useReserveAppointment.ts
├── tests
│   ├── appointmentMutations.test.tsx
│   └── useAppointments.test.tsx
├── types.ts
└── utils.ts

```

```ts
// src/components/appointments/hooks/useAppointments.ts 

import dayjs from "dayjs";  // date library, recommended :D
import { useState } from "react";

import { AppointmentDateMap } from "../types";
import { getAvailableAppointments } from "../utils";
import { getMonthYearDetails, getNewMonthYear } from "./monthYear";

import { useLoginData } from "@/auth/AuthContext";
import { axiosInstance } from "@/axiosInstance";
import { queryKeys } from "@/react-query/constants";

// for queryFn
async function getAppointments(
  year: string,
  month: string
): Promise<AppointmentDateMap> {
  const { data } = await axiosInstance.get(`/appointments/${year}/${month}`);
  //                                                      ^^^^^^^^^^^^^^^^ so there are two parameters for this endpoint
  return data;
}

// The purpose of this hook:
//   1. track the current month/year (aka monthYear) selected by the user
//     1a. provide a way to update state
//   2. return the appointments for that particular monthYear
//     2a. return in AppointmentDateMap format (appointment arrays indexed by day of month)
//     2b. prefetch the appointments for adjacent monthYears
//   3. track the state of the filter (all appointments / available appointments)
//     3a. return the only the applicable appointments for the current monthYear
export function useAppointments() {
  /** ****************** START 1: monthYear state *********************** */
  // get the monthYear for the current date (for default monthYear state)
  const currentMonthYear = getMonthYearDetails(dayjs());
  //                                           ^^^^^^^ get current date of dayjs.Dayjs type

  // state to track current monthYear chosen by user
  // state value is returned in hook return object
  const [monthYear, setMonthYear] = useState(currentMonthYear);  // track monthYear

  // setter to update monthYear obj in state when user changes month in view,
  // returned in hook return object
  function updateMonthYear(monthIncrement: number): void {
    setMonthYear((prevData) => getNewMonthYear(prevData, monthIncrement));  // setter of monthYear
  }
  /** ****************** END 1: monthYear state ************************* */
  /** ****************** START 2: filter appointments  ****************** */
  // State and functions for filtering appointments to show all or only available
  const [showAll, setShowAll] = useState(false);   // track filter state

  // We will need imported function getAvailableAppointments here
  // We need the user to pass to getAvailableAppointments so we can show
  //   appointments that the logged-in user has reserved (in white)
  const { userId } = useLoginData();  // when filter is on, we will use this userId to filter

  /** ****************** END 2: filter appointments  ******************** */
  /** ****************** START 3: useQuery  ***************************** */
  // useQuery call for appointments for the current monthYear

  // TODO: update with useQuery!
  // Notes:
  //    1. appointments is an AppointmentDateMap (object with days of month
  //       as properties, and arrays of appointments for that day as values)
  //
  //    2. The getAppointments query function needs monthYear.year and
  //       monthYear.month
  const appointments: AppointmentDateMap = {};

  /** ****************** END 3: useQuery  ******************************* */

  return { appointments, monthYear, updateMonthYear, showAll, setShowAll };
  //       ^^^^^^^^^^^^  ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ these track states are interacted with in the page
  //       ^^^^^^^^^^^^ query data, but results are effected by track states
}
```

In useAppointments, we use many functions related to the operation of monthYear. These functions are all designed to abstract the operation of monthYear, allowing useAppointments to focus on queries.

```ts
// src/components/appointments/hooks/monthYear.ts 

import dayjs from 'dayjs';

// for storing current month / year details
export interface MonthYear {
  startDate: dayjs.Dayjs; // first day of the month
  firstDOW: number; // day of week; 0 === Sunday
  lastDate: number; // last date of the month
  monthName: string; // name of the month
  month: string; // two digit month number
  year: string; // four digit year
}

// for incrementing MonthYear
export function getUpdatedMonthYear(
  monthYear: MonthYear,
  monthIncrement: number,
): dayjs.Dayjs {
  // the clone is necessary to prevent mutation
  return monthYear.startDate.clone().add(monthIncrement, 'months');
}

// get calendar-relevant data for the month containing initialDate
export function getMonthYearDetails(initialDate: dayjs.Dayjs): MonthYear {
  const month = initialDate.format('MM');
  const year = initialDate.format('YYYY');
  const startDate = dayjs(`${year}${month}01`);
  const firstDOW = Number(startDate.format('d'));
  const lastDate = Number(startDate.clone().endOf('month').format('DD'));
  const monthName = startDate.format('MMMM');
  return { startDate, firstDOW, lastDate, monthName, month, year };
}

export function getNewMonthYear(
  prevData: MonthYear,
  monthIncrement: number,
): MonthYear {
  // update the monthYear by the specified increment
  const newMonthYear = getUpdatedMonthYear(prevData, monthIncrement);

  // return object with the details for the new monthYear
  return getMonthYearDetails(newMonthYear);
}


```

Let's check the most important component in the appointments system:

```tsx
// src/components/appointments/Calendar.tsx 

import {
  Box,
  Checkbox,
  Grid,
  Heading,
  HStack,
  IconButton,
} from "@chakra-ui/react";
import dayjs from "dayjs";
import { TiArrowLeftThick, TiArrowRightThick } from "react-icons/ti";

import { DateBox } from "./DateBox";
import { useAppointments } from "./hooks/useAppointments";  // import

import { UserAppointments } from "@/components/user/UserAppointments";

export function Calendar() {
  const currentDate = dayjs();

  const { appointments, monthYear, updateMonthYear, showAll, setShowAll } = useAppointments();  // use

  return (
    <Box>
      <HStack mt={10} spacing={8} justify="center">
        <IconButton
          aria-label="previous month"
          onClick={() => updateMonthYear(-1)}  // interact with track state, it'll update appointments data
          icon={<TiArrowLeftThick />}
          isDisabled={monthYear.startDate < currentDate}
        />
        <Heading minW="40%" textAlign="center">
          {monthYear.monthName} {monthYear.year}
        </Heading>
        <IconButton
          aria-label="next month"
          onClick={() => updateMonthYear(1)}  // interact with track state, it'll update appointments data
          icon={<TiArrowRightThick />}
        />
        <Checkbox
          variant="flushed"
          width="48"
          position="absolute"
          right="10px"
          checked={!showAll}
          defaultChecked
          onChange={() => setShowAll((prevValue) => !prevValue)}  // interact with track state, it'll update appointments
        >
          Only show available
        </Checkbox>
      </HStack>
      <Grid templateColumns="repeat(7, 1fr)" gap={4} my={5} mx={10}>
        {/* first day needs a grid column */}
        <DateBox
          date={1}
          gridColumn={monthYear.firstDOW + 1}
          appointments={appointments[1]}
        />
        {/* the rest of the days will follow */}
        {[...Array(monthYear.lastDate)].map((_, i) =>
          i > 0 ? (
            <DateBox key={i} date={i + 1} appointments={appointments[i + 1]} />
          ) : null
        )}
      </Grid>
      <UserAppointments />
    </Box>
  );
}

```

The unique part is that the first DayBox must determine its position in the grid, which is determined by `monthYear.firstDOW`, and then the following DayBoxes are rendered using iteration.
