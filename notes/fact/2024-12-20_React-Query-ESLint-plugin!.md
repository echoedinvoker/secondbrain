---
date: 2024-12-20
type: fact
aliases:
  -
hubs:
  - "[[Tanstack]]"
---

# React Query ESLint plugin!


Since this course was published, React Query has come out with an [ESLint plugin](https://tanstack.com/query/v4/docs/react/eslint/eslint-plugin-query). This course doesn't use the plug-in, but I encourage you to add it to the course project and use it! 



## Applying plugin and lint rules to course code
[This commit on the React Query v4 branch](https://github.com/bonnie/udemy-REACT-QUERY/commit/2b78a9830c6bc1b7790e3ca2fc6bd7fc8a245b7b) shows an example of how to install and configure the React Query ESLint plug-in, and the changes to the code as a result. Most of the code changes were accomplished with ESLint auto-fix, but a couple dependencies needed hand fixing (commented).



## Lint rule details
**1.   @tanstack/query/exhaustive-deps**
This rule will highlight any query keys that don't include dependencies from the query function (similar to what the "react-hooks/exhaustive-deps" rule does for a useEffect dependency array in [eslint-plugin-react-hooks](https://www.npmjs.com/package/eslint-plugin-react-hooks)). See the [official docs](https://tanstack.com/query/v4/docs/react/eslint/exhaustive-deps) for more details.



**2. @tanstack/query/prefer-query-object-syntax**
Currently, there are two ways to supply arguments to `useQuery`:

Multiple arguments: 

```jsx
useQuery(queryKey, queryFn, {
  onSuccess,
});
```

A single object argument: 

```jsx
useQuery({
  queryKey,
  queryFn,
  onSuccess,
});
```


**Argument support in different versions**

In React Query 3 (the latest version when this course was written), only the "multiple arguments" syntax was supported.

In React Query 4, both syntax options are supported.

In React Query 5 (not yet released), only the object syntax will be supported (see "remove overloads" in the [React Query v5 Roadmap](https://github.com/TanStack/query/discussions/4252)).



This rule "future proofs" the useQuery syntax by requiring the object syntax, so there won't be errors when upgrading to React Query 5. It will also **automatically fix your code** and convert the "multiple arguments" syntax into the "object syntax."  See the [official docs](https://tanstack.com/query/v4/docs/react/eslint/prefer-query-object-syntax) for more details.
