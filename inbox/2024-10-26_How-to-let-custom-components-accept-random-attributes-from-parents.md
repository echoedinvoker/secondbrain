---
date: 2024-10-26
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# How to let custom components accept random attributes from parents

```tsx
// src/components/common/form-button.tsx
import { Button } from "@nextui-org/react";
import { useFormStatus } from "react-dom";

interface FormButtonProps {
  children: React.ReactNode;
}

export default function FormButton({ children }: FormButtonProps) {
  const { pending } = useFormStatus()

  return (
    <Button type="submit" isLoading={pending}>
      {children}
    </Button>
  )
}
```
above is a custom button component for forms, how to let it accept random attributes from parents

🤖:[ChatClaude-3-5-Sonnet]
To allow the FormButton component to accept random attributes from its parent components, you can use the spread operator and TypeScript's built-in utility types. Here's how you can modify the component to achieve this:

```tsx
// src/components/common/form-button.tsx
import { Button, ButtonProps } from "@nextui-org/react"; // ButtonProps contains all the prop types that the NextUI Button component accepts
import { useFormStatus } from "react-dom";
import React from "react";

interface FormButtonProps extends Omit<ButtonProps, 'isLoading'> {  // Because we are handling 'isLoading' internally, we should exclude it from the inherited props
  children: React.ReactNode;
}

export default function FormButton({ children, ...props }: FormButtonProps) {  // Use the spread operator to collect all other props
  const { pending } = useFormStatus()

  return (
    <Button type="submit" isLoading={pending} {...props}>  {/* Spread the additional props onto the Button component */}
      {children}
    </Button>
  )
}
```


Now, you can use the FormButton component like this:

```tsx
<FormButton color="primary" size="lg" className="my-custom-class"> {/* attribute `color`, `size`, `className` are passed to the Button component inside FormButton */}
  Submit
</FormButton>
```
