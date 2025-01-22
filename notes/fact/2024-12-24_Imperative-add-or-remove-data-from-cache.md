---
date: 2024-12-24
type: fact
aliases:
  -
hubs:
  - "[[Tanstack]]"
---

# Imperative add or remove data from cache

In [[2024-12-23_Query-user-object-when-log-in|this topic]], we fetched user data from the server when user logged in by using `useQuery` hook, which is declarative.

But sometimes we need to add or remove data from the cache **imperatively**. So, we want to create functions to update and remove data from the cache imperatively below:

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

  // imperatively update and remove data from the cache are methods of queryClient (such as prefetchQuery)
  const queryClient = useQueryClient();

  const { data: user } = useQuery({
    enabled: !!userId,
    queryKey: generateUserKey(userId, userToken),
    queryFn: () => getUser(userId, userToken),
    staleTime: Infinity,
  })

  // Imperative update user data from the cache
  function updateUser(newUser: User): void {
    queryClient.setQueryData(generateUserKey(userId, userToken), newUser);
    //                       ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ the key and data are ordered arguments instead of object argument
  }

  // Imperative remove user data from the cache
  function clearUser() {
    queryClient.removeQueries({
      queryKey: [queryKeys.user]  // only need to provide the first item of the queryKey
    });                           // all queries with the key that starts with queryKeys.user will be removed
  }

  return { user, updateUser, clearUser };
  //             ^^^^^^^^^^^^^^^^^^^^^ provide the methods to update and remove data from the cache imperatively
}
```

We'll use them in the `useAuthActions` hook to update and remove user data from the cache imperatively.

```ts
// src/auth/useAuthActions.tsx 

import axios, { AxiosResponse } from "axios";

import { User } from "@shared/types";

import { useLoginData } from "./AuthContext";

import { axiosInstance } from "@/axiosInstance";
import { useCustomToast } from "@/components/app/hooks/useCustomToast";
import { useUser } from "@/components/user/hooks/useUser";

interface UseAuth {
  signin: (email: string, password: string) => Promise<void>;
  signup: (email: string, password: string) => Promise<void>;
  signout: () => void;
}

type UserResponse = { user: User };
type ErrorResponse = { message: string };
type AuthResponseType = UserResponse | ErrorResponse;

export function useAuthActions(): UseAuth {
  const { updateUser, clearUser } = useUser();
  const { setLoginData, clearLoginData } = useLoginData();

  const SERVER_ERROR = "There was an error contacting the server.";
  const toast = useCustomToast();

  async function authServerCall(
    urlEndpoint: string,
    email: string,
    password: string
  ): Promise<void> {
    try {
      const { data, status }: AxiosResponse<AuthResponseType> =
        await axiosInstance({
          url: urlEndpoint,
          method: "POST",
          data: { email, password },
          headers: { "Content-Type": "application/json" },
        });

      if (status === 400) {
        const title = "message" in data ? data.message : "Unauthorized";
        toast({ title, status: "warning" });
        return;
      }

      if ("user" in data && "token" in data.user) {
        toast({
          title: `Logged in as ${data.user.email}`,
          status: "info",
        });

        // when signin, update user data to the cache imperatively
        // because we don't need to query user data again, so it'll let user feel less 'laggy' (compare to `useQuery`)
        updateUser(data.user);

        setLoginData({ userId: data.user.id, userToken: data.user.token });  // `useQuery` will fetch user data again, after this line (set context data) is executed
      }                                                                      // so it's slower than imperative way
    } catch (errorResponse) {
      const title =
        axios.isAxiosError(errorResponse) &&
        errorResponse?.response?.data?.message
          ? errorResponse?.response?.data?.message
          : SERVER_ERROR;
      toast({
        title,
        status: "error",
      });
    }
  }

  async function signin(email: string, password: string): Promise<void> {
    authServerCall("/signin", email, password);
  }
  async function signup(email: string, password: string): Promise<void> {
    authServerCall("/user", email, password);
  }

  function signout(): void {

    // clear user data from the cache imperatively
    // in order to prevent unauthorized access to user data
    clearUser();

    clearLoginData();
    toast({
      title: "Logged out!",
      status: "info",
    });
  }

  return {
    signin,
    signup,
    signout,
  };
}

```
