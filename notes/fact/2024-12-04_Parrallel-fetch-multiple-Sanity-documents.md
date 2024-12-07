---
date: 2024-12-04
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Parrallel fetch multiple Sanity documents

In [[2024-12-04_Extract-type-from-skills-schema-and-fetch-skills-data-on-the-frontend|this topic]], we fetch three different documents from Sanity with blocking methods. Because these three documents are independent of each other, we can fetch them in parallel to speed up the process:


**Blocking fetch:**

```ts
// dev-portfolio/src/routes/+page.ts

import type { PageLoad } from './$types';
import sanityClient, { processProjectEntries } from '$lib/utils/sanity'

export const load: PageLoad = async () => {

  // these three fetches are blocking, meaning that one will wait for the other to finish
  // then the next one will start to fetch
  const workExperience: DevExperience[] = await sanityClient.fetch(
    '*[_type == "devExperience"] | order(starDate desc)'
  )
  const rawProjects: SanityProject[] = await sanityClient.fetch(
    '*[_type == "project"] | order(dateAccomplished desc)'
  )

  const skills: Skill[] = await sanityClient.fetch(
    '*[_type == "skills"][0].skillsList'
  )

  const projects = rawProjects.map(processProjectEntries);

  return {
    workExperience,
    projects,
    skills
  }
};
```

```ts
import type { PageLoad } from './$types';
import sanityClient, { processProjectEntries } from '$lib/utils/sanity'

export const load: PageLoad = async () => {

  // we refactor the code to fetch three documents in parallel with Promise.all
  const [
    workExperience,
    rawProjects,
    skills
  ]: [
    DevExperience[],
    SanityProject[],
    Skill[]
  ] = await Promise.all([ // only ONE `await` in front of Promise.all now
    sanityClient.fetch(
      '*[_type == "devExperience"] | order(starDate desc)'
    ),
    sanityClient.fetch(
      '*[_type == "project"] | order(dateAccomplished desc)'
    ),
    sanityClient.fetch(
      '*[_type == "skills"][0].skillsList'
    )
  ]);

  const projects = rawProjects.map(processProjectEntries);

  return {
    workExperience,
    projects,
    skills
  }
};

```

This refactor should speed up the fetching process a bit ._.
