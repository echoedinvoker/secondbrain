---
date: 2024-12-22
type: fact
aliases:
  -
hubs:
  - "[[Tanstack]]"
---

# Global refetch settings

In [[2024-12-22_Refetch-to-treatments|this topic]], we configured refetch settings for the treatments data. However, we want to apply the same settings globally to all queries.

If we want to configure the refetch settings globally, queryClient has a `defaultOptions` property. We can set the default options for all queries:

```ts
// src/react-query/queryClient.ts

import { toast } from "@/components/app/toast";
import { QueryCache, QueryClient } from "@tanstack/react-query";

function errorHandler(errorMsg: string) {
  const id = "react-query-toast";

  if (!toast.isActive(id)) {
    const action = "fetch";
    const title = `could not ${action} data: ${errorMsg ?? "error connecting to server" }`;
    toast({ id, title, status: "error", variant: "subtle", isClosable: true });
  }
}

export const queryClient = new QueryClient({
  // configure re-fetch settings globally
  defaultOptions: {
    queries: {  // query options for all queries (including prefetchQuery)
      staleTime: 1000 * 60 * 10,
      gcTime: 1000 * 60 * 15,
      refetchOnWindowFocus: false,  // usually, we don't want to refetch on window focus, but reconnect and component mount are useful
    }
    // there are more options for mutations, we'll cover them in the future
  },

  queryCache: new QueryCache({
    onError: (error) => {
      errorHandler(error.message)
    }
  })
});

```

Now, all queries including `prefetchQuery` will have the same refetch settings. We don't need to set the refetch settings for each query.

And of course, we can override the global settings on a specific query if needed.

Currently, all project are using more relaxed refetch settings, but appointments data is very time sensitive, it'll update even user is not interacting with the app. So we want to override the refetch settings for the appointments data.
About user profile and user appointments, we want to refetch them on demand, which means we want to invalidate the cache when mutations are made.
