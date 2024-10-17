---
date: 2024-10-15
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Cache control   On Demand

```tsx
import { revalidatePath } from "next/cache";

// When we think data that the '/snippets'
// route uses has changed...
revalidatePath("/snippets");
```

**Limitation**

You must  know exactly **when** data changes and **where** the user expects to see up-to-date data.


**Clarification of Misconceptions**

Please note that this method updates the cached page on the server, rather than dynamically changing the front-end page. Therefore, the updated results will only be visible after a user requests "/snippets" after the update.

So this method is very suitable for use with [[2024-10-13_Redirect-in-the-action-function|redirect]].
