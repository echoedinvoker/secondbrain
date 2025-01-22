---
date: 2024-12-20
type: fact
aliases:
  -
hubs:
  - "[[Tanstack]]"
---

# Default Error fallback of queries

There is no `useQueryError` hook (like [[2024-12-20_Centralize-fetching-status-handling|useIsFetching]]) in React Query, but we can use `QueryCache` and `QueryClient` to set the default process for handling errors of queries.

```ts
// src/react-query/queryClient.ts 

import { toast } from "@/components/app/toast";
import { QueryCache, QueryClient } from "@tanstack/react-query";
      // ^^^^^^^^^^ we use its instance to set some default behavior of all queries

// create a error handler function to show a toast message (which is nothing to do with React Query)
function errorHandler(errorMsg: string) {
  // https://chakra-ui.com/docs/components/toast#preventing-duplicate-toast
  // one message per page load, not one message per query
  // the user doesn't care that there were three failed queries on the staff page
  //    (staff, treatments, user)
  const id = "react-query-toast";  // we can give the Chakra Toast a unique id to prevent duplicate toasts rendering

  // when the toast with the id is not active, show the toast
  if (!toast.isActive(id)) {
    const action = "fetch";
    const title = `could not ${action} data: ${errorMsg ?? "error connecting to server" }`;
    toast({ id, title, status: "error", variant: "subtle", isClosable: true });
  }
}

export const queryClient = new QueryClient({
  // use QueryCache to set default behavior of all queries, in this case, error handling
  queryCache: new QueryCache({
    onError: (error) => {
      errorHandler(error.message)  // use the fn we created above to show a toast message
    }
  })
});

```

Now, all queries will use above error handling function by default. We can also override this behavior by providing `onError` function in the `useQuery` hook specifically.


![toast-error.png](../assets/imgs/toast-error.png)
