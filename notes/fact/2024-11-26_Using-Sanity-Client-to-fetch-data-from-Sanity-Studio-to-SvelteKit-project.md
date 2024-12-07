---
date: 2024-11-26
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Using Sanity Client to fetch data from Sanity Studio to SvelteKit project

After we [[2024-11-25_Set-document-type-of-sanity-project|set document type of sanity project]], added some data in Sanity web dashboard and [[2024-11-26_Create-frontend-Sanity-client|created Sanity client]],we can use Sanity Client to fetch data from Sanity Studio in our SvelteKit project.


## Where to fetch data

But before fetching, we must consider whether the data to be fetched is sensitive information. If it is, we can only fetch in `+page.server.ts`, otherwise it should be done in `+page.ts`. Because the former ensures that it only runs on the backend, while the latter will run on both the front and back end.


## Fetching in `+page.ts`

Assuming that the data is not sensitive, we can fetch it in `+page.ts`. Here is an example of fetching data from Sanity Studio in a SvelteKit project:


```ts
// src/routes/+page.ts

import type { DevExperience } from '$lib/types/sanity';
import type { PageLoad } from './$types'; // this type may not exist until you run `npm run dev` or `npm run check`
import sanityClient from '$lib/utils/sanity'

export const load: PageLoad = async () => {
  const workExperience: DevExperience[] = await sanityClient.fetch(
    '*[_type == "devExperience"]'
    //           ^^^^^^^^^^^^^ this is name of the schema type we defined in Sanity Studio repo
  )

  return {
    workExperience
  }
};
```

```svelte
<!-- src/routes/+page.svelte -->

<script lang="ts">
  import { HeroSection, AboutSection } from "$components";

  let { data } = $props();
  //    ^^^^ returned data from load function will props to `data`, and it is already typed (after `npm run dev` or `npm run check`)

  $inspect(data);
</script>

<HeroSection />
<AboutSection />
```

```bash
init {
  workExperience: [
    {
      _createdAt: '2024-11-25T13:35:33Z',
      _type: 'devExperience',
      _updatedAt: '2024-11-26T03:28:24Z',
      startDate: '2018-01-01',
      endDate: '2019-01-01',
      jobTitle: 'Software Engineer',
      _rev: 'YxlPEZ906je9FlPCDVa0fL',
      company: 'Company B',
      _id: '3d6aa539-7f74-406a-8539-6b9c2630a460'
    },
    {
      _rev: 'm9UJ22wcZ3cY6SnOnkW4gx',
      _type: 'devExperience',
      company: 'Company A',
      _id: '8df55a47-0216-439c-b0e5-b2798fc405b0',
      _updatedAt: '2024-11-26T03:28:34Z',
      startDate: '2019-01-01',
      _createdAt: '2024-11-25T13:35:02Z',
      jobTitle: 'Software Engineer'
    },
    {
      _rev: 'm9UJ22wcZ3cY6SnOnkW3k1',
      _id: 'dd361fbb-5d1d-4761-844b-a7beb1d6a10c',
      startDate: '2017-01-01',
      _createdAt: '2024-11-25T13:36:00Z',
      jobTitle: 'Software Engineer',
      _type: 'devExperience',
      company: 'Company C',
      _updatedAt: '2024-11-26T03:28:15Z',
      endDate: '2018-01-01'
    },
    {
      _createdAt: '2024-11-25T13:36:24Z',
      _rev: 'lOOXzC16pMsOeS2C3fhOqf',
      _type: 'devExperience',
      company: 'Company D',
      endDate: '2017-01-01',
      jobTitle: 'Software Engineer',
      _id: 'fc1841f3-f219-4f2c-be4d-b72fb0ef40f7',
      _updatedAt: '2024-11-26T03:28:06Z',
      startDate: '2016-01-01'  # you can see the data order is not sorted, if you want to sort it by `startDate`...
    }
  ]
}
```

```ts
// src/routes/+page.ts

import type { DevExperience } from '$lib/types/sanity';
import type { PageLoad } from './$types';
import sanityClient from '$lib/utils/sanity'

export const load: PageLoad = async () => {
  const workExperience: DevExperience[] = await sanityClient.fetch(
    '*[_type == "devExperience"] | order(starDate desc)'
    //                          ^^^^^^^^^^^^^^^^^^^^^^^ add this to sort the fetched data by `startDate` field
  )

  return {
    workExperience
  }
};

```
```bash
init {
  workExperience: [
    {
      startDate: '2018-01-01',
      endDate: '2019-01-01',
      _createdAt: '2024-11-25T13:35:33Z',
      _rev: 'YxlPEZ906je9FlPCDVa0fL',
      _id: '3d6aa539-7f74-406a-8539-6b9c2630a460',
      jobTitle: 'Software Engineer',
      _type: 'devExperience',
      company: 'Company B',
      _updatedAt: '2024-11-26T03:28:24Z'
    },
    {
      startDate: '2019-01-01',
      _createdAt: '2024-11-25T13:35:02Z',
      jobTitle: 'Software Engineer',
      _rev: 'm9UJ22wcZ3cY6SnOnkW4gx',
      _type: 'devExperience',
      company: 'Company A',
      _id: '8df55a47-0216-439c-b0e5-b2798fc405b0',
      _updatedAt: '2024-11-26T03:28:34Z'
    },
    {
      endDate: '2018-01-01',
      _createdAt: '2024-11-25T13:36:00Z',
      jobTitle: 'Software Engineer',
      _updatedAt: '2024-11-26T03:28:15Z',
      _rev: 'm9UJ22wcZ3cY6SnOnkW3k1',
      _type: 'devExperience',
      company: 'Company C',
      _id: 'dd361fbb-5d1d-4761-844b-a7beb1d6a10c',
      startDate: '2017-01-01'
    },
    {
      jobTitle: 'Software Engineer',
      _rev: 'lOOXzC16pMsOeS2C3fhOqf',
      _id: 'fc1841f3-f219-4f2c-be4d-b72fb0ef40f7',
      _updatedAt: '2024-11-26T03:28:06Z',
      endDate: '2017-01-01',
      _createdAt: '2024-11-25T13:36:24Z',
      startDate: '2016-01-01',  # now the data is sorted by `startDate` field in descending order
      _type: 'devExperience',
      company: 'Company D'
    }
  ]
}

```

There are many other criteria you can find in the Sanity documentation to filter and sort data.

If we make sure that the data is all correct, we can start to [[2024-11-26_Replace-dummy-data-with-fetched-data-from-Sanity|replace dummy data with fetched data from Sanity]].
