---
date: 2024-10-21
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Render input error message with nextui elements

Nextui components `Input`, `Textarea`, `Select` and `Radio` have `isInvalid` and `errorMessage` props that can be used to render error messages directly. We don't need to use other elements like `p` or `span` to render error messages.

```tsx
'use client';

import { Button, Input, Textarea } from "@nextui-org/react";
import * as actions from "@/actions"
import { useFormState } from "react-dom";

export default function TopicCreateForm() {
  const [formState, action] = useFormState(actions.createTopic, { errors: {} });
  return (
    <form action={action}>
      <Input
        name="name"
        isInvalid={!!formState.errors.name}                   // this props only exists in nextui Input component
        errorMessage={formState.errors.name?.join(", ")}      // this props only exists in nextui Input component
      />
      <Textarea
        name="description"
        isInvalid={!!formState.errors.description}              // this props only exists in nextui Textarea component
        errorMessage={formState.errors.description?.join(", ")} // this props only exists in nextui Textarea component
      />
      <Button type="submit">submit</Button>
    </form>
  )
}
```

