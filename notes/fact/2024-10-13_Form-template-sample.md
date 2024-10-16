---
date: 2024-10-13
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Form template sample

```tsx
export default function SnippetCreatePage() {
  return (
    <form>
      <h3 className="font-bold m-3">Create a new snippet</h3>
      <div className="flex flex-col gap-4">
        <div className="flex gap-4">
          <label className="w-12" htmlFor="title">
            Title
          </label>
          <input
            name="title"
            className="border rounded p-2 w-full"
            id="title"
          />
        </div>
        <div className="flex gap-4">
          <label className="w-12" htmlFor="code">
            Code
          </label>
          <textarea
            name="code"
            className="border rounded p-2 w-full"
            id="code"
          />
        </div>

        <button type="submit" className="bg-blue-200 p-2 rounded">
          Create
        </button>
      </div>
    </form>
  );
}

```

**Label and input association**

label property `htmlFor` is used to associate the label with the input field. This is important for accessibility. The `id` of the input field should match the `htmlFor` value of the label.


**Submit button and input fields identification**

The submit button has the type `submit` which will submit the form when clicked. The input fields have a `name` property which is used to identify the input fields when the form is submitted.

This is default behavior in HTML forms. The input fields are sent in the request body when the form is submitted. Next.js has a built-in API on top of it.
