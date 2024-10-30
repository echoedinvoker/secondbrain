---
date: 2024-10-19
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Validate form submit with zod

Zod is a popular TypeScript-first schema declaration and validation library. We can simply use it in normal JavaScript or Next.js [[2024-10-13_Action-function|action]].

**Installation**

```bash
npm install zod

```

**Collect user inputs from the form**

```tsx
// src/components/topics/topic-create-form.tsx 
import * as actions from "@/actions"

export default function TopicCreateForm() {
  return (
    <form action={actions.createTopic}>
      <input name="name" />                 {/* property `name` is required for mutating action's form data */}
      <textarea name="description" />
      <button type="submit">submit</Button>
    </form>
  )
}

```

**Create a validator object with zod and validate the form data**

```ts
// src/actions/create-topic.ts 
'use server';

import { z } from "zod";

const createTopicSchema = z.object({  // define the validation rules for the form data, it will return a validator object
  name: z.string().min(3).regex(/^[a-z-]+$/, { message: "name must be lowercase and hyphenated" }),
  description: z.string().min(10).max(100), // only regex need to define the error message, others will use the default error message
});

export async function createTopic(formData: FormData) {
  try {
    const result = createTopicSchema.parse({  // Use the validator object to validate the form data
      name: formData.get("name"),
      description: formData.get("description"),
    });
    console.log(result);  // when the data is valid, it will log the data here
  } catch (error) { // when the data is invalid, it will throw an z.ZodError, we can catch it here
    if (error instanceof z.ZodError) {
      console.log(error.flatten().fieldErrors); // default error contents are hard to use, we can flatten it to a more readable format
    }
  }
}

```

**Test the form validation**

test by name="$" and description="abc", we got the console logs:
```bash
# invalid data
{
  name: [
    'String must contain at least 3 character(s)', # list all the error messages in the name field
    'name must be lowercase with hyphens'          # we may use useFormState to show the error messages in the form
  ],
  description: [ 'String must contain at least 10 character(s)' ]  # list all the error messages in the description field
}

```
[[2024-10-15_How-to-do-error-handling-without-JS|useFormState]]

test by name="abc" and description="aaaaaaaaaaaaaaaaaaaaa", we got the console logs:
```bash
{ name: 'abc', description: 'aaaaaaaaaaaaaaaa' }  # valid data, we can use it to create a new topic in this case

```

[[2024-10-20_Validate-form-inputs-in-server-action-and-render-errors-on-the-frontend|here]] is a simple example to validate form inputs in the server action and render errors on the frontend.
