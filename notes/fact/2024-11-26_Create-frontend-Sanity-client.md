---
date: 2024-11-26
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Create frontend Sanity client

After [[2024-11-25_Sanity-Setup|Sanity Setup]], we need to create a frontend client to fetch data from the Sanity Studio.

[https://www.npmjs.com/package/sanity]
[https://www.sanity.io/docs/js-client]

The link above provides instructions on how to install and generate a sanity client on the front end:

```bash
~/D/g/s/s/dev-portfolio > npm i sanity
#         ^^^^^^^^^^^^^ install sanity frontend libray in our main project
```

```ts
// src/lib/utils/sanity.ts 

import { createClient, type ClientConfig } from '@sanity/client'

const config: ClientConfig = {
  projectId: 'sccxiprj',  // you can find this in file `sanity.cli.ts` in the sanity studio repo
  dataset: 'production', // in free tier, you are only allowed to use `production` dataset
  useCdn: true, // to distribute data via the Sanity CDN
  apiVersion: '2024-11-26', // use current date (YYYY-MM-DD) to target the latest API version
}

const client = createClient(config)
export default client
//             ^^^^^^ then, you can use this client to fetch data from Sanity Studio anywhere in your main project

```
