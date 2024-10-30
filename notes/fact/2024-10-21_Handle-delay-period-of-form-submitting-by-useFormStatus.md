---
date: 2024-10-21
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Handle delay period of form submitting by useFormStatus

```tsx
// src/components/item/item-create-form.tsx
import * as actions from "@/actions"

export default function CreateItemForm() {
  return (
    <form action={actions.createItem}>  {/* This is a very simple form, which uses the server action to create an item */}
      <input name="name" />             {/* But the server action may take a while to process, we need to show a loading indicator */}
      <textarea name="description" />
      <button>Submit</button>
    </form>
  )
}

```

`react-dom` provides a hook called `useFormStatus` to handle the state when submitting a form, but it cannot be placed within the component where the form is located. Therefore, we create a child component below to use it:

```tsx
// src/components/common/form-button.tsx 
'use client';

import { Button } from "@nextui-org/react";
import { useFormStatus } from "react-dom";

interface FormButtonProps {
  children: React.ReactNode;
}

export default function FormButton({ children }: FormButtonProps) {
  const { pending } = useFormStatus()  // This hook will detect the very closest form in the parent component and return the status of the form

  return (
    <Button type="submit" isLoading={pending}>{children}</Button>  // `pending` is usually false, but it will be true when the form is submitting
  )                                                                // So we can easily create a loading indicator with it
}

```

Then, replace the submit button in the form with the `FormButton` component, and you will see the loading indicator when submitting the form.
