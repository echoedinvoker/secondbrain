---
date: 2024-11-02
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Communication between components with prop rune

```svelte src/routes/+page.svelte
<script>
  import UserInfo from '$lib/components/UserInfo.svelte';
</script>

<UserInfo userName="John Doe">  
<!--      ^^^^^^^^^^^^^^^^^^  prop `userName` is passed to the component -->
      <p>Some text</p>
<!--  ^^^^^^^^^^^^^^^^  prop `children` are passed to the component -->
</UserInfo>

```

```svelte src/lib/components/UserInfo.svelte 
<script>
  const data = $props();  // data will get a object: {userName: 'John Doe', children: <p>Some text</p>}
</script>

<p>{data.userName}</p>

      {@render data.children()}
<!--  ^^^^^^^^^^^^^^^^^^^^^^^^^  In svlete, children are passed as a function, and we can use @render to render them -->

```

In Svelte, children will be passed as a [[2024-11-02_Using-snippet-in-the-svelte-file|snippet function]], so we can use `@render` to call it.

We can **destructure** props to make the code cleaner:

```svelte src/lib/components/UserInfo.svelte 
<script>
  const {userName, children, ...props} = $props();
//      ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^  Destructuring props, ...props will get all the props except userName and children
</script>

<p>{data.userName}</p>

{@render data.children()}

```

With `TypeScript`, we should always define the type of props:

```svelte src/lib/components/UserInfo.svelte 
<script lang="ts">  // lang="ts" is required for TypeScript
  import type {Snippet} from 'svelte';

  interface UserInfoProps {
    userName: string;
    children: Snippet;   // Snippet is a type for children function
    [key: string]: any;  // this is for ...props
  }
  const {userName, children, ...props}: UserInfoProps = $props();
</script>

<p>{data.userName}</p>

{@render data.children()}

```


**We can also define props in the page component**

>In the page component, props can also be defined, but there are some differences compared to other components. The page component will automatically define props as an object containing two properties: `data` and `form`.
