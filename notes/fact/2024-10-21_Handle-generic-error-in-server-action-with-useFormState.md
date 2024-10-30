---
date: 2024-10-21
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Handle generic error in server action with useFormState

The server action below omits the handling of fields validation and error messages, only showing how to handle errors other than fields, such as database errors and user authentication errors.

```ts
'use server';

import type { Item } from "@prisma/client";
import { auth } from "@/auth";
import { db } from "@/db";
import { redirect } from "next/navigation";
import paths from "@/paths";

interface CreateItemFormState {
  errors: {
    // fields errors
    _form?: string[];   // for generic error
  }
}

export async function createItem(formState: CreateItemFormState, formData: FormData): Promise<CreateItemFormState> {

  // validate form fields inputs... and return an `result` object containing the valid fields values

  // check if the user is logged in
  const session = await auth()
  if (!session || !session.user) {
    return { errors: { _form: ["You must be logged in to create a topic"] } } as CreateTopicFormState;
  }          // if the user is not logged in, return an error message by form state property `_form`

  let item: Item | null;
  try {
    item = await db.item.create({   // use try catch block to handle the database operation error
      data: {
        slug: result.name,
        description: result.description,
      }
    });
  } catch (error: unknown) {  // if an error occurs, catch the error and return an error message by form state property `_form`
    if (error instanceof Error) {
      return { errors: { _form: [error.message] } } as CreateItemFormState;
    }
    return { errors: { _form: ["An unknown error occurred"] } } as CreateItemFormState;
  }

  redirect(paths.topicShow(topic.slug));
}

```
Next, how to display error messages on the frontend is similar to the way we display [[2024-10-21_Render-input-error-message-with-nextui-elements|field errors]].
