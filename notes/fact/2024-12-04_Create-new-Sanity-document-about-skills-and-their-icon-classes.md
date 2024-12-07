---
date: 2024-12-04
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Create new Sanity document about skills and their icon classes

Now, we need to start creating the skills section of the landing page. This section will use icons to represent different skills. We need a new Sanity document for users to add skills and their corresponding icons themselves.

About icons, we use the `devicon` library as below:


![devicons.png](../assets/imgs/devicons.png)

```html
<!-- dev-portfolio/src/app.html  -->

<!doctype html>
<html lang="en">

<head>
  <meta charset="utf-8" />
  <link rel="icon" href="%sveltekit.assets%/favicon.png" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  %sveltekit.head%
  <link rel="preconnect" href="https://fonts.googleapis.com" />
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
  <link href="https://fonts.googleapis.com/css2?family=Inter+Tight:ital,wght@0,100..900;1,100..900&display=swap"
    rel="stylesheet" />
  
  <!-- paste from devicon website, install devicon library(CSS) in our main project -->
  <link rel="stylesheet" type='text/css' href="https://cdn.jsdelivr.net/gh/devicons/devicon@latest/devicon.min.css" />
</head>

<body data-sveltekit-preload-data="hover">
  <div style="display: contents">%sveltekit.body%</div>
</body>

</html>
```

Then, we need to define the schema of the new document, which need to have fields to represent the skill name and the icon class. 

```ts
// studio-dev-portfolio-course/schemaTypes/skill.ts 

import { defineType, defineField } from "sanity";

export const skills = defineType({
  name: "skills",
  title: "Skills",
  type: "document",
  fields: [
    // only one main fields to store the list of skills
    defineField({
      name: "skillsList",
      title: "Skill List",
      type: "array", // so the type should be array of objects
      of: [
        {
          type: 'object',
          fields: [
            defineField({
              name: 'name',
              title: 'SkillName',
              type: 'string',
              validation: (rule) => rule.required()  // each fields of object are required
            }),                                      // but main field is not required because empty array is valid
            defineField({
              name: 'iconClass',
              title: 'Icon Class Name',
              type: 'string',
              validation: (rule) => rule.required()
            })
          ]
        }
      ]
    }),
  ]
})
```
Remember include this new schema in the `schemaTypes/index.ts` file.

```ts
// studio-dev-portfolio-course/schemaTypes/index.ts 

import { devExperience } from "./devExperience";
import { project } from "./project";
import { skills } from "./skill";  // import the new schema we just created

export const schemaTypes = [devExperience, project, skills];
//                                                  ^^^^^^ add the new schema

```
Then, launch the Sanity Studio and add some skills data to the new document and push it to the cloud:

![add-skills-doc.png](../assets/imgs/add-skills-doc.png)

Don't forget to click `publich` button to push data to the cloud...
