---
date: 2024-12-17
type: fact
aliases:
  -
hubs:
  - "[[Tanstack]]"
---

# Setup React Query


## install react-query

```bash
npm i @tanstack/react-query
```

## Create a react-query client and props it to the QueryClientProvider

```jsx
// src/App.jsx 

import { Posts } from "./Posts";
import "./App.css";
import { QueryClient, QueryClientProvider } from "@tanstack/react-query";  // import 

const queryClient = new QueryClient(); // create a react-query client, which contains the cache and tools

function App() {
  return (

    // We don't access the client directy, but prop it to the QueryClientProvider, which is a context provider
    <QueryClientProvider client={queryClient}>
    {/* So that any descendants can use react-query hooks to interact with the client */}

      <div className="App">
        <h1>Blog &apos;em Ipsum</h1>
        <Posts />
      </div>

    </QueryClientProvider>
  );
}

export default App;


