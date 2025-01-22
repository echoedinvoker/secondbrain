---
date: 2024-12-17
type: fact
aliases:
  -
hubs:
  - "[[Tanstack]]"
---

# React Query Dev Tools

```bash
npm i @tanstack/react-query-devtools
```

```jsx
// src/App.jsx 

import { Posts } from "./Posts";
import "./App.css";
import { QueryClient, QueryClientProvider } from "@tanstack/react-query";
import { ReactQueryDevtools } from "@tanstack/react-query-devtools";  // import

const queryClient = new QueryClient();

function App() {
  return (
    <QueryClientProvider client={queryClient}>
      <div className="App">
        <h1>Blog &apos;em Ipsum</h1>
        <Posts />
      </div>
      <ReactQueryDevtools />  {/* add */}
    </QueryClientProvider>
  );
}

export default App;

```

```bash
npm run dev
# for vite, when run script `dev`, it will set `NODE_ENV` to `development`
# and the React Query Dev Tools will be available only in development mode.

# if using `npm run build`, vite will set `NODE_ENV` to `production`
# so the React Query Dev Tools will not be available in production mode.

```

![rq-devtool.png](../assets/imgs/rq-devtool.png)

