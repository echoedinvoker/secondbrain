---
date: 2024-10-13
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Action function

Action function is a function that is executed on the server, not on the browser. 

```tsx
export default function demoPage() {  // Must be a server component

  async function demoFn(args) {  // args will be pass to the backend server

    'use server'  // this is a magic comment to make the function run on the server, and this function must be async with it

    console.log(args); // this will be printed on the server console, not on the browser console
      
  }

  return ( ... );
}

```

It is usefull when you want to do something on the server, like [[2024-10-13_Run-database-CRUD-in-the-action-function|saving data to the database]] , sending email, etc. 


**When we want to create or mutate data, we should consider using the action function at first.**

That's because the action function is executed on the server-side, and it is more secure than the client-side. 


**Action function con only be defined in the server component, but the client component can call it**

Even client component cannot define the action function, it can still call the action function defined in the server component. There are two options to realize it.

1. [[2024-10-14_Props-server-action-to-the-client-component|Define it in the server component and props it to the client component]]
2. [[2024-10-14_Define-server-action-in-a-seperate-file|Define server action in a seperate file and import it to the client component]]



