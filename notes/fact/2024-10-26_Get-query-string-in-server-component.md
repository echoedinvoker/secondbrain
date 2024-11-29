---
date: 2024-10-26
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Get query string in server component

To get the query string in a server component, it must be `page component` (page.tsx). Page components get query string props automatically.

```tsx
interface PageProps {
  searchParams: Promise<{  // `searchParams` is a special prop that contains the query string parameters
    term: string;          // assume there is a query string parameter named 'term'
  }>;                      // URL is like /page?term=hello
}

function Page({ searchParams }: PageProps) {
  const { term } = await searchParams;  // Get the query string parameter 'term', keyword `await` is required
                                                                                  // (Next15 update)
  return (
    <div>
      <h1>Search term: {term}</h1>  {/* Output: Search term: hello */}
    </div>
  );
}

```

But if you use `searchParams` reference in a page component, its route will become a `dynamic` route.

If you really want the route to be static, you can [[2024-10-26_Get-query-string-in-client-component|access the query string in a client component]] by hook `useSearchParams`.
