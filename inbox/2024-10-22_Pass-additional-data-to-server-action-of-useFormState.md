---
date: 2024-10-22
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Pass additional data to server action of useFormState

Before, we have discussed how to [[2024-10-15_Using-form-action-to-call-server-action-in-the-client-component|pass additional data to the server action]].

Below is a more complex example. If the server action is placed inside useFormState, how can additional parameters be passed to it?

```tsx
export default function PostCreateForm({ slug }: PostCreateFormProps) {  // assume slug is additional data we need to pass it to the server action
  const [formState, action] = useFormState(actions.createPost.bind(null, slug), { errors: {} });
  return (                                                   // still using `bind` to pass additional data to the server action
    // ...
  )
}

```

But, now the arguments of server action are not only the form data and form state, but also the additional data `slug`. We need to modify the server action to fix the type error.

```ts
'use server';

export async function createPost(slug: string, formData: FormData, formState: FormState) {
                             // ^^^^^
                             // bind data into the first argument
  // ...
}

```

This requirement usually happens when we need to create an item related to other entities. [[2024-10-22_Create-a-item-which-related-to-other-entities|Here]] is an example.
