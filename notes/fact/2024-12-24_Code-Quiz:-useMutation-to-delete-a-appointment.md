---
date: 2024-12-24
type: fact
aliases:
  -
hubs:
  - "[[Tanstack]]"
---

# Code Quiz: useMutation to delete a appointment


## Code Quiz

- Implement in `/src/components/appointments/hooks/useCancelAppointment.ts`
* very similar to `useReserveAppointment`
* **onSuccess:** invalidate appointment queries, show toast
* This hook doesn't need `useLoginData`
    * unsetting, so don't need `userId`
    * delete function does check for user token already


## Solution on my own

```ts
// src/components/appointments/hooks/useCancelAppointment.ts 

import { Appointment } from "@shared/types";

import { axiosInstance } from "@/axiosInstance";
import { useCustomToast } from "@/components/app/hooks/useCustomToast";
import { queryKeys } from "@/react-query/constants";
import { useMutation, useQueryClient } from "@tanstack/react-query";

// fn to remove the appointment from the user
async function removeAppointmentUser(appointment: Appointment): Promise<void> {
  const patchData = [{ op: 'remove', path: '/userId' }];
  await axiosInstance.patch(`/appointment/${appointment.id}`, {
    data: patchData,
  });
} // ...lecture is a bit lazy to do token check on server side, so don't use this in production

// Implementation of the useCancelAppointment hook
export function useCancelAppointment() {
  const toast = useCustomToast();
  const queryClient = useQueryClient()

  const { mutate } = useMutation({
    mutationFn: (appointment: Appointment) => removeAppointmentUser(appointment),
    onSuccess: () => {
      queryClient.invalidateQueries({
        queryKey: [queryKeys.appointments] // query filter, which invalidates the cache of `useAppointments` and `useAppointmentsByDate` hooks
      });
      toast({ title: "Appointment cancelled", status: "warning" });
      //                                               ^^^^^^^ instead of success, use warning for cancel action
    }
  })

  return mutate;
}

```

We found that the arguments of `mutate` are the same with the arguments of `removeAppointmentUser`, so we can simplify the code:

```ts
import { Appointment } from "@shared/types";

import { axiosInstance } from "@/axiosInstance";
import { useCustomToast } from "@/components/app/hooks/useCustomToast";
import { queryKeys } from "@/react-query/constants";
import { useMutation, useQueryClient } from "@tanstack/react-query";

async function removeAppointmentUser(appointment: Appointment): Promise<void> {
  const patchData = [{ op: 'remove', path: '/userId' }];
  await axiosInstance.patch(`/appointment/${appointment.id}`, {
    data: patchData,
  });
}

export function useCancelAppointment() {
  const toast = useCustomToast();
  const queryClient = useQueryClient()

  const { mutate } = useMutation({
    mutationFn: removeAppointmentUser,
    //          ^^^^^^^^^^^^^^^^^^^^^ when arguments are the same, we can simplify the code
    onSuccess: () => {
      queryClient.invalidateQueries({
        queryKey: [queryKeys.appointments]
      });
      toast({ title: "Appointment cancelled", status: "warning" });
    }
  })

  return mutate;
}

```
