---
date: 2024-12-24
type: fact
aliases:
  -
hubs:
  - "[[Tanstack]]"
---

# Code Quiz: useUserAppointments

Although user appointments data can also be considered as part of user data, unlike user data which hardly changes within a session, appointments data changes with the user's behavior. Therefore, the lecturer handles the two separately.


## Code Quiz

- Implement `useQuery` in _src/components/users/hooks/useUserAppointments.ts_
- Dependent on `userId` being truthy (from `useLoginData`)
- Create a factory function for the query key
- Return a query key that starts with `[queryKeys.appointments, queryKeys.user]` (it'll be more clear why it's more associative to appointments later)
- Remove data from the query cache on user sign out **imperatively**


## Solution on my own


```ts
// src/components/user/hooks/useUserAppointments.ts 

import type { Appointment } from "@shared/types";

import { axiosInstance, getJWTHeader } from "../../../axiosInstance";

import { useLoginData } from "@/auth/AuthContext";
import { useQuery } from "@tanstack/react-query";
import { queryKeys } from "@/react-query/constants";

async function getUserAppointments(
  userId: number,
  userToken: string
): Promise<Appointment[] | null> {
  const { data } = await axiosInstance.get(`/user/${userId}/appointments`, {
    headers: getJWTHeader(userToken),
  });
  return data.appointments;
}

export function useUserAppointments(): Appointment[] {

  const { userId, userToken } = useLoginData();

  const { data = [] } = useQuery({
    enabled: !!userId,
    queryKey: [queryKeys.appointments, queryKeys.user, userId, userToken],
    queryFn: () => getUserAppointments(userId, userToken),
    staleTime: Infinity, // because appointments only change when the user does something
  })                     // So, I think there is no any re-fetching needed

  return data;
}

```

Because the queryKey is way too complex, I think it's better to create a factory function for it.

```ts
// src/react-query/key-factories.ts

import { queryKeys } from "./constants"

export const generateUserKey = (userId: number, userToken: string) => {
  return [queryKeys.user, userId, userToken];
}

// create a factory function for the query key
export const generateUserAppointmentsKey = (userId: number, userToken: string) => {
  return [queryKeys.appointments, queryKeys.user, userId, userToken];
}
```

```ts
// src/components/user/hooks/useUserAppointments.ts

import type { Appointment } from "@shared/types";

import { axiosInstance, getJWTHeader } from "../../../axiosInstance";

import { useLoginData } from "@/auth/AuthContext";
import { useQuery } from "@tanstack/react-query";
import { generateUserAppointmentsKey } from "@/react-query/key-factories"; // import

async function getUserAppointments(
  userId: number,
  userToken: string
): Promise<Appointment[] | null> {
  const { data } = await axiosInstance.get(`/user/${userId}/appointments`, {
    headers: getJWTHeader(userToken),
  });
  return data.appointments;
}

export function useUserAppointments(): Appointment[] {

  const { userId, userToken } = useLoginData();

  const { data = [] } = useQuery({
    enabled: !!userId,
    queryKey: generateUserAppointmentsKey(userId, userToken),
    //        ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ use key factory function
    queryFn: () => getUserAppointments(userId, userToken),
    staleTime: Infinity,
  })

  return data;
}
```

Next, we need to remove user appointments data from the cache imperatively when the user signs out. We can do it in the `clearUser` function of `useUser` hook.

```ts
// src/components/user/hooks/useUser.ts

import { AxiosResponse } from "axios";

import type { User } from "@shared/types";

import { axiosInstance, getJWTHeader } from "../../../axiosInstance";

import { useLoginData } from "@/auth/AuthContext";
import { useQuery, useQueryClient } from "@tanstack/react-query";
import { generateUserKey } from "@/react-query/key-factories";
import { queryKeys } from "@/react-query/constants";

async function getUser(userId: number, userToken: string) {
  const { data }: AxiosResponse<{ user: User }> = await axiosInstance.get(
    `/user/${userId}`,
    {
      headers: getJWTHeader(userToken),
    }
  );

  return data.user;
}

export function useUser() {
  const { userId, userToken } = useLoginData();
  const queryClient = useQueryClient();

  const { data: user } = useQuery({
    enabled: !!userId,
    queryKey: generateUserKey(userId, userToken),
    queryFn: () => getUser(userId, userToken),
    staleTime: Infinity,
  })

  function updateUser(newUser: User): void {
    queryClient.setQueryData(generateUserKey(userId, userToken), newUser);
  }

  function clearUser() {
    queryClient.removeQueries({
      queryKey: [queryKeys.user]
    });

    // remove user appointments data from the cache imperatively
    queryClient.removeQueries({
      queryKey: [queryKeys.appointments, queryKeys.user]
    });
  }

  return { user, updateUser, clearUser };
}

```
