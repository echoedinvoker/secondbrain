---
date: 2024-10-15
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# How to do error handling without JS

We use the hook `useFormState` to handle form error handling, so that we can handle errors in the form without needing to use JS to handle errors.

And because we need to use the `useFormState` hook, we need to turn component into client component:

```tsx
'use client'

import { useFormState } from "react-dom";  // useFormState is the key to let backend communicate with frontend, and it comes from react-dom
import * as actions from '@/actions'

export default function ClientComponent() {
  const [formState, action] = useFormState(actions.fn, { message: '' })  // we need to pass in initial state { message: '' } here, just like a normal state
  // action is transformed from actions.fn, it adds arg 'formState': action.fn(formData) => action(formState, formData)
  // and the returned value will be used to update formState. (that's how it give the backend the way to communicate back to the frontend)

  return (
    <div>
      <form action={action}>  {/* we replace actions.fn with action here, so that the backend can communicate back to the frontend */}
        <button type="submit">Submit</button>
      </form>
      <div>{formState.message}</div>  {/* formState is just like a normal state, so this component will re-render when formState changes */}
    </div>
  )
}

```

**This can work without JS**

Although useFormState is a hook, it saves the formState in the HTML form. So when the action is executed, the formState will be sent to the backend. After the backend processes it, the new formState will be sent back. This way, the formState can be updated on the frontend without using JavaScript.


**Error handling (or validation) in the backend**

In this case, handling error or validation codes should be written in the action.fn, so these tasks are all handled in the backend server. This is very different from how we used to handle errors and validation in a React app.
