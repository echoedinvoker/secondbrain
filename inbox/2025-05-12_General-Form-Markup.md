---
date: 2025-05-12
type: fact
aliases:
  -
hubs:
  - "[[vue]]"
---

# General Form Markup

Below is genaral form markup of different input types with Tailwind CSS classes.

```vue
<template>
  <div> <!-- If we use <Transition> to wrap this component, we need to ensure that this component has only one root element -->
    <h2 class="text-2xl mb-4">General</h2>
    <form class="space-y-4 mx-auto">
      <div>
        <label>Username</label>
        <input type="text" />
      </div>
      <div>
        <label>Email</label>
        <input type="email" />
      </div>
      <div>
        <label>About Me</label>
        <textarea></textarea>
      </div>
      <div>
        <label>Gender</label>
        <label>
          <input type="radio" value="male" /> <!-- v-model these three radio inputs to the same Ref -->
          <span>Male</span>                   <!-- then, they will become a group (single selection) -->
        </label>
        <label>
          <input type="radio" value="female" />
          <span>Female</span>
        </label>
        <label>
          <input type="radio" value="other" />
          <span>Other</span>
        </label>
      </div>
      <div>
        <label>Country</label>
        <select>
          <option>USA</option>
          <option>Canada</option>
          <option>UK</option>
        </select>
      </div>
      <button type="submit" class="btn-primary">Save</button>
    </form>
  </div>
</template>
```

```css
/* main.css  */
@import "tailwindcss";

/* define styles globally (not recommended for production) */
label {
  @apply flex items-center gap-1;
}

input[type='text'], /* use [type='text'] to avoid conflicts with other input types, only effecting text inputs */
input[type='email'] {
  @apply bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-full p-2.5;
}

textarea {
  @apply bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-full p-2.5;
}

input[type='radio'] {
  @apply w-4 h-4 border border-gray-300 rounded bg-gray-50 focus:ring-4 focus:ring-blue-300 mr-2;
}

select {
  @apply bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-full p-2.5;
}

.btn-primary {
  @apply text-white bg-blue-700 hover:bg-blue-800 focus:ring-4 focus:outline-none focus:ring-blue-300 rounded-md text-sm w-full sm:w-auto px-5 py-2 text-center;
}
```

Above, we use `@apply` and markup selectors to apply Tailwind CSS classes globally. This is not recommended for production code because it's hard to modify and maintain. Instead, we should use Tailwind CSS classes directly in the markup. However, this is a good way to get started with Tailwind CSS and see how it works.

