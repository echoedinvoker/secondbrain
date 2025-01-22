---
date: 2024-12-21
type: fact
aliases:
  -
hubs:
  - "[[Tanstack]]"
---

# Options for pre-populating data

`Pre-populating data` means that before using a certain data, values are stored in cache (or not cache) in advance.

|   | Where to use? | Data from? | Added to cache? |
|---|---|---|---|
| `prefetchQuery` | `queryClient` | server | yes |
| `setQueryData` | `queryClient` | client | yes |
| `placeholderData` | `useQuery` | client | no |
| `initialData` | `useQuery` | client | yes |

- `queryClient.setQueryData` can be used to set data when we got response from server to a mutation, it save a fetch to the server.

- `placeholderData` is a option in `useQuery` hook. It replace the fallback we used before:

```jsx
  const fallback: Staff[] = [];

  const { data: staff = fallback } = useQuery({
    //                ^^^^^^^^^^ before we use fallback to prevent the error from components
    queryKey: [queryKeys.staff],
    queryFn: getStaff,
  })
```

```jsx
  const fallback: Staff[] = [];

  const { data: staff, isPlaceholderData } = useQuery({
    //                 ^^^^^^^^^^^^^ it also provide this property to let us know
    //                               if current data is placeholderData or not
       
    queryKey: [queryKeys.staff],
    queryFn: getStaff,
    placeholderData: fallback,  // now we can use placeholderData instead of fallback
  })
```

- `initialData` is similar to `placeholderData`, but it will be added to cache.
