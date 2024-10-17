---
date: 2024-10-15
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Handling server action error by useFormState mechanism

The core principle of this approach is to catch the error and return the error information to the frontend normally instead of raising an exception.

```tsx
'use server';

export async function serverAction(formState: { message: string }) { // we can use the formState to pass the error information to the frontend
  try {

    throw new Error('This is an error');  // assume this is an unexpected error
                                          // we must catch it to prevent it raising an exception
  } catch (error: unknown) {
    if (error instanceof Error) {
      return { error: error.message };    // return the error information normally instead of raising an exception
    } else {
      return { error: 'An unexpected error occurred' };
    }
  }
}

```

**Benefits:**

1. The page will keep the same one, so all stats will be kept.


**BE CAREFUL WITH REDIRECTIONS**

>If the [[2024-10-13_Redirect-in-the-action-function|redirect]] is used in the server action to redirect, the `redirect` statement should not be placed inside a try-catch block, because the mechanism of `redirect` generates a special error that must be caught by the server in order to redirect correctly. If it is caught by the try-catch block first, the redirection will not be done correctly.
