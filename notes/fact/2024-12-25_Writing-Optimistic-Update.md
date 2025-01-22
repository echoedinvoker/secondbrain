---
date: 2024-12-25
type: fact
aliases:
  -
hubs:
  - "[[Tanstack]]"
---

# Writing Optimistic Update

In [[2024-12-25_Update-cache-with-Mutation-response|this topic]], we update cache with mutation response. But UI rendering is slower than form insertion because of the network delay. So, we can use [[2024-12-25_Intro-of-Optimistic-Updates-in-React-Query|optimistic Updates]] to update the UI instantly and then update the cache with the response.


![weird-ux-for-update.png](../assets/imgs/weird-ux-for-update.png)

## Implementing Optimistic Update

```ts
// src/components/user/hooks/usePatchUser.ts 

import jsonpatch from "fast-json-patch";

import type { User } from "@shared/types";

import { axiosInstance, getJWTHeader } from "../../../axiosInstance";
import { useUser } from "./useUser";
import { useMutation, useQueryClient } from "@tanstack/react-query";
import { useCustomToast } from "@/components/app/hooks/useCustomToast";
import { queryKeys } from "@/react-query/constants";

export const MUTATION_KEY = 'patch-user'  // we need to use `useMutationState` to get the mutation below
                                          // so we need a key to filter the mutation
async function patchUserOnServer(
  newData: User | null,
  originalData: User | null,
): Promise<User | null> {
  if (!newData || !originalData) return null;
  const patch = jsonpatch.compare(originalData, newData);

  const { data } = await axiosInstance.patch(
    `/user/${originalData.id}`,
    { patch },
    {
      headers: getJWTHeader(originalData.token),
    },
  );
  return data.user;
}

export function usePatchUser() {
  const { user, updateUser } = useUser();
  //            ^^^^^^^^^^ we don't use this, it can be remove here
  const toast = useCustomToast();
  const queryClient = useQueryClient();  // we need queryClient to invalidate the cache

  const { mutate: patchUser } = useMutation({
    mutationKey: [MUTATION_KEY], // used by useMutationState to filter the mutation
    mutationFn: (newData: User) => patchUserOnServer(newData, user),
    // onSuccess: (userData: User | null) => {    // with optimistic update, we don't update the cache immediately
      // updateUser(userData);                    // we always use invalidateQueries to update the cache when using optimistic updates
    onSuccess: () => {
      toast({ title: "User updated", status: "success" });
    },
    // we MUST use `onSettled` to invalidate the cache, so when the mutation is failed, it can rollback automatically
    onSettled: () => {
      return queryClient.invalidateQueries({ queryKey: [queryKeys.user] });
    //^^^^^^ returning promise is very important here, it can make this mutation to keep `pending` until the onSettled is resolved
    }
  })

  return patchUser;
}

```

Then, we can use the `useMutationState` to get the mutation and use it to update the UI optimistically.

```tsx
// src/components/user/UserProfile.tsx 

import {
  Box,
  Button,
  Flex,
  FormControl,
  FormLabel,
  Heading,
  Input,
  Stack,
} from "@chakra-ui/react";
import { Field, Form, Formik } from "formik";
import { useEffect } from "react";
import { useNavigate } from "react-router-dom";

import { MUTATION_KEY, usePatchUser } from "./hooks/usePatchUser";
import { useUser } from "./hooks/useUser";
import { UserAppointments } from "./UserAppointments";

import { useLoginData } from "@/auth/AuthContext";
import { useMutationState } from "@tanstack/react-query";
import { User } from "@shared/types";

export function UserProfile() {
  const { userId } = useLoginData();
  const { user } = useUser();
  const patchUser = usePatchUser();
  const navigate = useNavigate();

  // filter out the pending mutation with the key MUTATION_KEY with `useMutationState`
  const pendingData = useMutationState({
    filters: { mutationKey: [MUTATION_KEY], status: "pending" },  // try to filter out the mutation we want, so the resault will be an array of mutation
    select: (mutation) => mutation.state.variables as User // mutation data is huge, we only need the variables so we can use `select` filter the information we need
  })

  // convert the array of mutation to the first element
  const pendingUser = pendingData ? pendingData[0] : null;

  useEffect(() => {
    if (!userId) {
      navigate("/signin");
    }
  }, [userId, navigate]);

  const formElements = ["name", "address", "phone"];
  interface FormValues {
    name: string;
    address: string;
    phone: string;
  }

  return (
    <Flex minH="84vh" textAlign="center" justify="center">
      <Stack spacing={8} mx="auto" w="xl" py={12} px={6}>
        <UserAppointments />
        <Stack textAlign="center">
          <Heading>Information for {pendingUser ? pendingUser.name : user?.name}</Heading>
                                {/* ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ when mutation is initiated, we use `pendingUser` to render the name instead of waiting for the server response */}
        </Stack>
        <Box rounded="lg" bg="white" boxShadow="lg" p={8}>
          <Formik
            enableReinitialize
            initialValues={{
              name: user?.name ?? "",
              address: user?.address ?? "",
              phone: user?.phone ?? "",
            }}
            onSubmit={(values: FormValues) => {
              patchUser({ ...user, ...values });
            }}
          >
            <Form>
              {formElements.map((element) => (
                <FormControl key={element} id={element}>
                  <FormLabel>{element}</FormLabel>
                  <Field name={element} as={Input} />
                </FormControl>
              ))}
              <Button mt={6} type="submit">
                Update
              </Button>
            </Form>
          </Formik>
        </Box>
      </Stack>
    </Flex>
  );
}
```

## Testing Optimistic Update

![update-opt-test.png](../assets/imgs/update-opt-test.png)
