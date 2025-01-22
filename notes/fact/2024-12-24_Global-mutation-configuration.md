---
date: 2024-12-24
type: fact
aliases:
  -
hubs:
  - "[[Tanstack]]"
---

# Global mutation configuration

Before we created a global spinner and global error handling for all queries, now we want to do the same for mutations.

```ts
// src/react-query/queryClient.ts

import { toast } from "@/components/app/toast";
import { MutationCache, QueryCache, QueryClient } from "@tanstack/react-query";
//       ^^^^^^^^^^^^^

// function errorHandler(errorMsg: string) {
function errorHandler(title: string) {
  const id = "react-query-toast";

  if (!toast.isActive(id)) {
    // const action = "fetch";
    // const title = `could not ${action} data: ${errorMsg ?? "error connecting to server"
    //   }`;
    toast({ id, title, status: "error", variant: "subtle", isClosable: true });
  }
}

// because we want to use above fucntion for mutations as well, so we extract some code to here for creating title for toast dynamically
function createTitle(errorMsg: string, actionType: 'query' | 'mutation') {
  const action = actionType === 'query' ? 'fetch' : 'update';
  return `could not ${action} data: ${errorMsg ?? "error connecting to server"}`;
}

export const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      staleTime: 1000 * 60 * 10,
      gcTime: 1000 * 60 * 15,
      refetchOnWindowFocus: false,
    }
  },
  queryCache: new QueryCache({
    onError: (error) => {
      const title = createTitle(error.message, 'query'); // use new function `createTitle` to create title for query error
      errorHandler(title)
    }
  }),

  // global error handling for mutations is very similar to queries, but use MutationCache instead
  mutationCache: new MutationCache({
    onError: (error) => {
      const title = createTitle(error.message, 'mutation') // use new function `createTitle` to create title for mutation error
      errorHandler(title)
    }
  })
});
```
```tsx
// src/components/app/Loading.tsx 

import { Spinner, Text } from "@chakra-ui/react";
import { useIsFetching, useIsMutating } from "@tanstack/react-query";

export function Loading() {
  const isFetching = useIsFetching();
  const isMutating = useIsMutating();  // similar to useIsFetching, but for mutations
  const display = isFetching || isMutating ? "inherit" : "none";
  //                         ^^^^^^^^^^^^^ so we use the same spinner for both fetching and mutating

  return (
    <Spinner
      thickness="4px"
      speed="0.65s"
      emptyColor="olive.200"
      color="olive.800"
      role="status"
      position="fixed"
      zIndex="9999"
      top="50%"
      left="50%"
      transform="translate(-50%, -50%)"
      display={display}
    >
      <Text display="none">Loading...</Text>
    </Spinner>
  );
}

```
