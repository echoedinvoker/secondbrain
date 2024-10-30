---
date: 2024-10-20
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Fix tsc error of useFormState

When we use useFormState, we often encounter tsc errors at the beginning, this is because of the inconsistency of types in the process of passing formState.

```tsx
'use client'

import { useFormState } from "react-dom";  
import * as actions from '@/actions'

export default function ClientComponent() {
  const [formState, action] = useFormState(
    actions.fn, // there is an tsc error appears on the `action.fn`
    { message: '' }  // thats because of this state is not consistent during the process
  ) 

  return (
    <div>
      <form action={action}>  
        <button type="submit">Submit</button>
      </form>
      <div>{formState.message}</div>  
    </div>
  )
}

```

```ts
'use server';

export async function fn(
  formState: { message: string }, // formState must be the same type as 2nd argument of useFormState
  formData: FormData
) {

  // ... some async logic

  return { message: 'success' }  // return value must be the same type as 1st argument of useFormState
}
```
```
