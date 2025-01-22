---
date: 2024-12-25
type: fact
aliases:
  -
hubs:
  - "[[Tanstack]]"
---

# Update cache with Mutation response

After successfully mutating, we use setQueryData to imperatively update the cache instead of invalidating it, so as to avoid refetching the data.

```ts
// src/components/user/hooks/usePatchUser.ts 

import jsonpatch from "fast-json-patch";

import type { User } from "@shared/types";

import { axiosInstance, getJWTHeader } from "../../../axiosInstance";
import { useUser } from "./useUser";
import { useMutation } from "@tanstack/react-query";
import { useCustomToast } from "@/components/app/hooks/useCustomToast";

// fetch function to server
async function patchUserOnServer(newData: User | null, originalData: User | null): Promise<User | null> {
  if (!newData || !originalData) return null;

  // create a patch for the difference between newData and originalData
  const patch = jsonpatch.compare(originalData, newData);

  // send patched data to the server
  const { data } = await axiosInstance.patch(
    `/user/${originalData.id}`,
    { patch },
    { headers: getJWTHeader(originalData.token) },
  );
  return data.user;
}

// useMutation hook and update cache of user data after successful mutation
export function usePatchUser() {
  const { user, updateUser } = useUser();
  //      ^^^^  ^^^^^^^^^^ we have created this function to update user cache immediately with `seQueryData`
  //      ^^^^ because fetch function needs original user data

  const toast = useCustomToast();

  const { mutate: patchUser } = useMutation({
    mutationFn: (newData: User) => patchUserOnServer(newData, user),
    //           ^^^^^^^^^^^^^                                ^^^^ original user data
    //           new user data is passed when this function is called

    onSuccess: (userData: User | null) => {
      //                  ^^^^^^^^^^^ to match return type of fetch function

      updateUser(userData); // use function from `useUser` hook to update user cache imperatively

      toast({ title: "User updated", status: "success" });
    }
  })

  return patchUser;
}

```
