---
date: 2024-12-04
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Extract type from skills schema and fetch skills data on the frontend

In [[2024-12-04_Create-new-Sanity-document-about-skills-and-their-icon-classes|this topic]], we already prepared the schema for the skills document in Sanity Studio and added some skills with their corresponding icons data to the cloud.

Now, we need to extract the type of the skills document from the schema:

```bash
cd studio-dev-portfolio-course/
sanity schema extract # Extract the schema to schema.json
npx sanity typegen generate # Generate Typescript types from the schema.json

```

```ts
// studio-dev-portfolio-course/sanity.types.ts

// (...)

// generated types from schema skills
export type Skills = {
  _id: string
  _type: 'skills'
  _createdAt: string
  _updatedAt: string
  _rev: string
  skillsList?: Array<{  // we only need this field on the fontend
    name?: string
    iconClass?: string
    _key: string
  }>
}

// (...)
```

Then, we can paste this type into our main project:

```ts
// dev-portfolio/src/lib/types/sanity.d.ts 

// (...)

// instead of paste the whole type direct, we remove private fields
// and make it a single type(remove Array<>, Skills -> Skill)
export type Skill = {
  name: string
  iconClass: string
  _key: string
}
```

```ts
// dev-portfolio/src/routes/+page.ts

import type { PageLoad } from './$types';
import sanityClient, { processProjectEntries } from '$lib/utils/sanity'

export const load: PageLoad = async () => {
  const workExperience: DevExperience[] = await sanityClient.fetch(
    '*[_type == "devExperience"] | order(starDate desc)'
  )
  const rawProjects: SanityProject[] = await sanityClient.fetch(
    '*[_type == "project"] | order(dateAccomplished desc)'
  )

  // fetch skills data
  const skills: Skill[] = await sanityClient.fetch(
  //            ^^^^^^^ instead of type Skills, Skill[] is much better
    '*[_type == "skills"][0].skillsList'
    //                   ^^^^^^^^^^^^^^ skills document only have one entry so we add [0] to get it directly in the query string
    //                                  and we only need data of skillsList field, so we add it to the end of the query string also
  )

  const projects = rawProjects.map(processProjectEntries);

  return {
    workExperience,
    projects,
    skills  // add skills to return object to pass it to the frontend
  }
};
```

Then, we can use $props to get the data in the frontend and check it with $inspect:

```svelte
<!-- dev-portfolio/src/routes/+page.svelte  -->

<script lang="ts">
  import { HeroSection, AboutSection } from "$components";
  import MyWorkSection from "$components/MyWorkSection.svelte";

  let { data } = $props();
  let { workExperience, projects, skills } = data;
  //                              ^^^^^^

  $inspect(skills); // check if the data is fetched correctly
</script>

<HeroSection />
<AboutSection {workExperience} />
<MyWorkSection {projects} />

```

We should see the data we added in Sanity Studio in the console:

```bash
init [
  {
    name: 'JavaScript',
    _key: '4e977562f98b',
    iconClass: 'devicon-javascript-plain'
  },
  {
    name: 'Python',
    _key: '97e9ee681182',
    iconClass: 'devicon-python-plain'
  },
  {
    iconClass: 'devicon-typescript-plain',
    name: 'Typescript',
    _key: '43f24b0b9ed5'
  },
  {
    name: 'React',
    _key: '96529806a050',
    iconClass: 'devicon-react-original'
  },
  {
    name: 'Vue',
    _key: 'd03eb10ecad0',
    iconClass: 'devicon-vuejs-plain'
  },
  {
    name: 'Svelte',
    _key: '5d76b6b253fa',
    iconClass: 'devicon-svelte-plain'
  },
  {
    name: 'Ansible',
    _key: 'f030502d1a3a',
    iconClass: 'devicon-ansible-plain'
  },
  {
    name: 'Kubernetes',
    _key: '2969cce0d834',
    iconClass: 'devicon-kubernetes-plain'
  },
  {
    name: 'Docker',
    _key: '0deb893f9524',
    iconClass: 'devicon-docker-plain'
  }
]

```
