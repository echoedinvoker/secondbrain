---
date: 2024-11-02
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Svelte API endpoint

In [[2024-11-02_Svelte-Data-Fetching-to-Frondend-diagram|Svelte Data Fetching to Frondend diagram]], we find that +page.server.ts and +page.client.ts must be executed before +page.svelte.

But sometime we need to trigger some server-side processing after page is loaded in the browser such as deleting a record from the database.

For this, we can create an API endpoint in the Svelte project and call it from the Svelte page.

And SvelteKit API endpoints can also let external services call your server-side code, bellow is a simple example:

```bash
~/Documents/gith/std/sv5/demo: tree src/routes/                                                                                                                                西元2024年11月02日 20時56分01秒

src/routes/
├── api  # `api` is a special directory in SvelteKit
│   └── purchase-confirmation   # folder name under `api` is the endpoint path, so this is `/api/purchase-confirmation` endpoint
│       └── +server.ts          # write the logic of the endpoint in this file, it will be executed on the server
├── blog
│   ├── [articleId]
│   │   ├── +page.svelte
│   │   └── +page.ts
│   └── +page.svelte
└── +page.svelte

```

```ts src/routes/api/purchase-confirmation/+server.ts                                                                                             西元2024年11月02日 20時56分08秒

import { json } from "@sveltejs/kit";  // used to send JSON response to the client

export async function POST({ request }) {
//                    ^^^^ `POST` is the method of the endpoint

  const requestBody = await request.json();
//                                 ^^^^^^^^ get the request body by method `json` of the request object, it is asynchronous

  // Do something with the request body
  console.log(requestBody);

  return json({ message: "confirmed" });  // response to the client which called the endpoint
}                                         // client can come from the same SvelteKit project or external service

```

Here I try to call the endpoint by nushell command (external service):

```bash
~/Documents/gith/std/sv5/demo: http post http://localhost:5173/api/purchase-confirmation -H [Content-Type application/json] `{ "id": "1" }`
╭─────────┬───────────╮
│ message │ confirmed │  # response from the endpoint
╰─────────┴───────────╯
```

bellow is the log of the server:

```bash
{ id: '1' }

```
