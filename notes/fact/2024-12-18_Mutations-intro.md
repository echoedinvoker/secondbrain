---
date: 2024-12-18
type: fact
aliases:
  -
hubs:
  - "[[Tanstack]]"
---

# Mutations intro


## React Query mutations

Making a network call that changes data on the server (update, delete, ...)

Because we use jsonplaceholder API as data source this chapter, so it just provides a fake response and doesn't really change the data on the server. So the following topics of this chapter will be just a introduction of how to use mutations of React Query but not showing changes to users.

In the chapter of Day Spa project, we will demonstrate showing changes to users in different ways:
1. Optimistic updates (assume change will happen)
2. Update React query cache with data returned from the server
3. Trigger re-fetch of relevant data (invalidation)


## useMutation

In React Query, we use `useMutation` hook to make a network call that changes data on the server.

It's similar to useQuery but:
1. returns `mutate` function
2. doesn't need query key (because it doesn't need to cache the data)
3. No `isFetching` and `isLoading`, only `isPending` (because it doesn't need to cache the data)
4. by default, no retries (useQuery has 3 retries by default)

