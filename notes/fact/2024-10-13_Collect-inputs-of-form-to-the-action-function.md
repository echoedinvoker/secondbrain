---
date: 2024-10-13
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Collect inputs of form to the action function

In Next.js, we pass a [[2024-10-13_Action-function|action function]] to the `action` attribute of the form tag to call a function when the form is submitted. This function will accept the form data as an argument. We can collect the values of the inputs by giving them a name attribute and using the `FormData.get()` method.

```tsx
export default function SnippetCreatePage() {

  async function createSnippet(formData: FormData) {  // this fn will accept the form data as an argument
    'use server'
    const title = formData.get('title') as string; // get the value of the input with the name attribute
    const code = formData.get('code') as string; // formData.get() returns a FormDataEntryValue, which is a File or string, so we need to cast it to string

    console.log(title, code);
  }
  return (
    <form action={createSnippet}>  {/* action attribute is used to call a function when the form is submitted, this function must be action function */}
      <h3 className="font-bold m-3">Create a new snippet</h3>
      <div className="flex flex-col gap-4">
        <div className="flex gap-4">
          <label className="w-12" htmlFor="title">Title</label>
          <input name="title" className="border rounded p-2 w-full" id="title" /> {/* value will be collected with the name attribute */}
        </div>
        <div className="flex gap-4">
          <label className="w-12" htmlFor="code">Code</label>
          <textarea name="code" className="border rounded p-2 w-full" id="code" /> {/* value will be collected with the name attribute */}
        </div>

        <button type="submit" className="bg-blue-200 p-2 rounded">Create</button>
      </div>
    </form>
  );
}

```
