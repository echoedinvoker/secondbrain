---
date: 2024-10-14
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Props server action to the client component

Server action can only be defined in the server component. So, if you want to use server action in the client component, you can define the server action in the server component and pass it as a prop to the client component.

```tsx
export const ServerComponent = () => {
  const serverAction = () => {  // define server action in the server component
    'use server'
    console.log('Server action');
  };
  return <ClientComponent onSubmit={serverAction} />; {/* pass server action as a prop */}
};


'use client';
export const ClientComponent = (props) => {
  return <button onClick={props.onSubmit}>Submit</button>;  {/* call server action as a event handler */}
};
```

> In normal circumstances, the server component cannot pass props event handlers to the client component, but the situation with the server action above is a special case.

