---
date: 2024-12-03
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Get Sanity project data by slug and pass it to frontend

After finish [[2024-11-28_Styling-MyWork-section|MyWork section]], we want to start to create project pages which links of each project in MyWork section will lead to.

So we need to create new route for project pages with dynamic parameter `slug`:
```bash
~/D/g/s/s/dev-portfolio > tree src/
src/
├── app.css
├── app.d.ts
├── app.html
├── assets
├── lib
└── routes
    ├── +layout.svelte
    ├── +page.svelte
    ├── +page.ts
    └── work  # <--- new folder for project pages
        └── [slug]  # <--- dynamic route
            ├── +page.svelte # <--- page component (frontend)
            └── +page.ts # load function (frontend/backend),
                         # fetch project data from sanity by slug
```

```ts
// src/routes/work/\[slug\]/+page.ts 

import sanityClient, { processProjectEntries } from "$lib/utils/sanity";
import { error } from "@sveltejs/kit";
import type { PageLoad } from "./$types";

export const load: PageLoad = async ({ params }) => {
                                  //   ^^^^^^ we can access dynamic params by
                                  //          destructuring `params` from PageLoad context
  const { slug } = params;  // and get exactly the `slug` param from it

  const rawProjects: SanityProject[] = await sanityClient.fetch(
    `*[_type == "project" && slug == $slug]`,
    //                    ^^^^^^^^^^^^^^^^ this is like `where` clause in SQL
    //                                     it means property `slug` of project should be equal to $slug
    { slug }  // and this is the value of $slug
  );
  if (rawProjects.length !== 1) {
    throw error(404, "Project not found");
//  ^^^^^^^^^^^ in load function, we throw `error` function from `@sveltejs/kit` to return 404 status code
//              and redirect user to 404 page if there is no project with that slug
  }

  const rawProject = rawProjects[0];
  //                            ^^^ sanityClient.fetch returns array of projects, there is no way to get single project directly
  const project = processProjectEntries(rawProject);
  //              ^^^^^^^^^^^^^^^^^^^^^ this is the function we created for processing raw project data
  //                                    to get more usable data for frontend

  return {
    project,
  };
}
```
```svelte
<!-- src/routes/work/\[slug\]/+page.svelte  -->

<script lang="ts">
  let { data } = $props();
  $inspect(data);   // in current stage, we just want to check if we get the project data correctly
</script>
```

We should see the project data in the console when we visit the project page:

```js
{
    "project": {
        "name": "project name 1",
        "company": "Company A",
        "slug": "project-1",
        "projectImageUrl": "https://cdn.sanity.io/images/sccxiprj/production/2877ff65399d1a14e94b36f093149d1277ffd3bf-783x589.png",
        "dateAccomplished": "2024-11-01",
        "stack": [
            "test1"
        ],
        "content": [
            {
                "type": "text",
                "style": "h1",
                "textToRender": "Test"
            },
            {
                "type": "text",
                "style": "normal",
                "textToRender": "test"
            },
            {
                "type": "image",
                "url": "https://cdn.sanity.io/images/sccxiprj/production/2033922342048753249bb65d458d12015583f4ae-787x984.png"
            },
            {
                "type": "text",
                "style": "normal",
                "textToRender": ""
            }
        ]
    }
}
```
