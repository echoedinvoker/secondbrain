---
date: 2024-12-24
type: fact
aliases:
  -
hubs:
  - "[[Tanstack]]"
---

# invalidate queries and query filters

In [[2024-12-24_useMutation-for-reserving-a-appointment|this topic]], we updated the appointment on the server but we did not update the query cache. This is a problem because the query cache still contains the old data. We can invalidate the query cache by using the `invalidateQueries` function from `react-query`.


## invalidateQueries

• Invalidate appointments cache information on mutation, so user doesn't have to refresh the page
• `invalidateQueries` effects:
    • marks query as stale
    • triggers re-fetch if query is active

• reference: https://tanstack.com/query/latest/docs/react/guides/query-invalidation


## Query filters

• Query client methods can affect multiple queries by using query filters
    • `removeQueries`, `invalidateQueries`, `cancelQueries`, etc

• These methods use `queryKey` also but it is actually query filters.

• Query filters can filter by:
    • query key (including partial match, but only at the start)
    • type: active, inactive or all
    • stale status, isFetching status

• Reference:
    https://tanstack.com/query/latest/docs/react/guides/filters#query-filters


## Implementation

```ts
// src/components/appointments/hooks/useReserveAppointment.ts

import { Appointment } from "@shared/types";

import { useLoginData } from "@/auth/AuthContext";
import { axiosInstance } from "@/axiosInstance";
import { useCustomToast } from "@/components/app/hooks/useCustomToast";
import { queryKeys } from "@/react-query/constants";
import { useMutation, useQueryClient } from "@tanstack/react-query";

async function setAppointmentUser(
  appointment: Appointment,
  userId: number | undefined,
): Promise<void> {
  if (!userId) return;
  const patchOp = appointment.userId ? 'replace' : 'add';
  const patchData = [{ op: patchOp, path: '/userId', value: userId }];
  await axiosInstance.patch(`/appointment/${appointment.id}`, {
    data: patchData,
  });
}

export function useReserveAppointment() {
  const { userId } = useLoginData();
  const toast = useCustomToast();

  const queryClient = useQueryClient();  // get the query client

  const { mutate } = useMutation({
    mutationFn: (appointment: Appointment) =>
      setAppointmentUser(appointment, userId),
    onSuccess: () => {
      // use queryClient method `invalidateQueries` to invalidate queries
      queryClient.invalidateQueries({
        queryKey: [queryKeys.appointments]
      //^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ this is `query filter` which target to all queries starting with `queryKeys.appointments`
      //                                   that includes `useAppointments` and `useUserAppointments` queries, so both will be invalidated
      //                                   and that's the reason that we put queryKeys.appointments to the most associative place in the queryKey array when we built `useUsreAppointments` query
      })

      toast({ title: "Appointment reserved", status: "success" });
    }
  })

  return mutate;
}
```

## Testing

![click-reserved-fix.png](../assets/imgs/click-reserved-fix.png)

If you want to more accurately determine whether there are updates, you can also use the React Query devtool to observe:

![check-devtool-for-mutate.png](../assets/imgs/check-devtool-for-mutate.png)


