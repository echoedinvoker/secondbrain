---
date: 2024-10-25
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Duplicated queries and cache system

This is the third cache mechanism of [[2024-10-15_There-are-four-type-of-Caches-in-NextJS|four type of Caches in NextJS]].

Assume we have a page:

```tsx
function Page() {
  const data = await getData("2") // duplicated query method
  return <div>
    <Profile id="1" />
    <Sittings id="1" />
  </div>
}

function Profile({ id }) {
  const data = await getData(id)  // duplicated query method and argument
  return <div>{data}</div>
}

function Sittings({ id }) {
  const data = await getData(id) // duplicated query method and argument
  return <div>{data}</div>
}

```

**Performance issue:**

In general, it is obvious that this page will have three `getData` queries loaded, which can cause performance issues. Therefore, NextJS provides a simple method to solve this problem.


**Execute the unique query once and cache the result for the same query:**

Every time the loading page, Next.js will check if the method and parameters of each query are the same. If there are two queries with both the same method and parameters, Next.js will only execute that query once, and other identical queries will directly use the result of the first query.


**Cache clearing between loading:**

Between loading, the cache will be cleared, so there is no issue of different users' caches affecting each other.


**How to use:**

>This mechanism is built into the `fetch` function only. If using other query functions, you need to handle it yourself using `cache`. Below is a simple example:

```tsx
export async function getData(id) {
  const data = await db.query(`SELECT * FROM users WHERE id = ${id}`) // this query method is not `fetch`
  return data                                                         // so we should use `cache` to refactor it
}

```

1. change to arrow function

```tsx
export async getData = (id) => {
  return cache.get(id) || await fetchData(id)
}

```

2. wrap entire arrow function with cache

```tsx
export async getData = cache(async (id) => {
  return await fetchData(id)
})

```

then, the cache mechanism will work as expected.

This is a [[2024-10-25_Duplicated-queries-in-recursive-components|use case]] of this mechanism.
```
