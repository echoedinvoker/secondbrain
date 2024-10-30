---
date: 2024-10-20
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Validate form inputs in server action and render errors on the frontend

Here is a simple example to demonstrate using [[2024-10-13_Action-function|server action]] to validate form input and return error messages to the frontend for rendering using the [[2024-10-15_Handling-server-action-error-by-useFormState-mechanism|useFormState mechanism]]. And we also use library [[2024-10-19_Validate-form-submit-with-zod|zod]] to validate form input.

```tsx
// src/components/create-item-form.tsx
'use client';  // to use useFormState, this component must be client component

import * as actions from "@/actions"
import { useFormState } from "react-dom";

export default function FormComponent() {
  const [formState, action] = useFormState(
    actions.createItem,
    { errors: {} }  // initial form state with no errors, it will pass through the action function
  );                // if there is validation error, the action function will return new form state with error fields
  return (
    <form action={action}>
        <input name="name" />
        {formState.errors.name && <p>{formState.errors.name?.join(', ')}</p>}   {/* render error message if errors has name property */}
        <textarea name="description" />
        {formState.errors.description && <p>{formState.errors.description?.join(', ')}</p>}  {/* render error message if errors has description property */}
        <button type="submit">submit</Button>
    </form>
  )
}

```

Above, we used native HTML elements for demonstration. If using nextui components, the error message does not need to be wrapped in a separate p tag. Instead, you can directly use relevant props on the nextui Input and TextArea components to display error messages. [[2024-10-21_Render-input-error-message-with-nextui-elements|Here]] is the example.

```ts
// src/actions/create-item.ts
'use server';

import { z } from "zod";

const createItemSchema = z.object({
  name: z.string().min(3).regex(/^[a-z-]+$/, { message: "name must be lowercase with hyphens" }),
  description: z.string().min(10).max(100),
});

interface CreateItemFormState {  // Define the form state
  errors: {
    name?: string[];    // this property exists only when name input has error
    description?: string[];  // this property exists only when description input has error
  }
}

export async function createItem(
  formState: CreateItemFormState,
  formData: FormData): Promise<CreateItemFormState> {
  try {
    createItemSchema.parse({
      name: formData.get("name"),
      description: formData.get("description"),
    });
    return { errors: {} };  // no error situation, no properties `name` and `description`
  } catch (error) {
    return {
      errors: (error as z.ZodError).flatten().fieldErrors   // validation failed, return error messages which include `name` or `description`
    } as CreateItemFormState;
  }
}

```
Only the part about fields validation is introduced above. If you want to handle generic errors, please refer to [[2024-10-21_Handle-generic-error-in-server-action-with-useFormState|here]].
