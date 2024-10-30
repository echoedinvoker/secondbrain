---
date: 2024-10-18
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Signout Issue with Next 14_2

In the upcoming lecture, we will be refactoring and creating a client HeaderAuth component. If you are using the latest version of Next 14.2+ with Next Auth you will find that the signout will not reflect in the UI without a hard refresh. As mentioned in [[2024-10-15_Next-14.2-is-causing-issues-with-next-auth|an earlier note]], this is a known bug.


A workaround that you can implement would be to import the signOut function from next-auth/react and use it in conjunction with the server action:



```tsx
import { signOut as nextAuthSignOut } from "next-auth/react";
 
// ...
 
<form
  action={async () => {
    await actions.signOut();
    await nextAuthSignOut({ redirect: false });
  }}
>

```
