---
date: 2024-11-02
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Svelte Data Fetching to Frondend diagram


### Backend:
**+page.server.ts**
   - Server Side ONLY!!!
   - Contains Server Load Function
   
### Backend | Frontend
**+page.ts**
   - Contains Load Function
   - Runs twice:
     - Once on the server
     - Once on the client

### Frontend Components:
**+page.svelte**
   - Should NOT contain any data fetching logic
   - Just renders the data coming from the load function in +page.ts or +page.server.ts


## Flow:
+page.server.ts → +page.ts → +page.svelte
