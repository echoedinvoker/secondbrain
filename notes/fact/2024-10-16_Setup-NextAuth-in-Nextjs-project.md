---
date: 2024-10-16
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Setup NextAuth in Nextjs project

```ts
// src/auth.ts
import { PrismaAdapter } from "@auth/prisma-adapter";
import NextAuth from "next-auth";
import GitHub from "next-auth/providers/github";
import { db } from "./db";

// Extract the client id and secret from the environment variables
const GITHUB_CLIENT_ID = process.env.GITHUB_CLIENT_ID;
const GITHUB_CLIENT_SECRET = process.env.GITHUB_CLIENT_SECRET;

// types of extracted variables can be undefined, so we need to verify them
if (!GITHUB_CLIENT_ID || !GITHUB_CLIENT_SECRET) {
  throw new Error('GITHUB_CLIENT_ID and GITUB_CLIENT_SECRET must be provided');
}

// Extract lots of utilities from NextAuth, we will use them in our entire application
export const { handlers: { GET, POST }, auth, signIn, signOut } = NextAuth({
  adapter: PrismaAdapter(db), // We use PrismaAdapter to let next-auth use prisma as the database
  providers: [
    GitHub({   // We use Github provider for authentication, so here we need to provide the client id and secret
      clientId: GITHUB_CLIENT_ID,
      clientSecret: GITHUB_CLIENT_SECRET
    })
  ],
  // Usually not needed, here we just fix a bug in next-auth
  callbacks: {
    async session({ session, user }: any) {
      if (session && user) {
        session.user.id = user.id;  // so the bug is that session.user.id is not set, so we set it here
      }

      return session;
    }
  }
})

```

`PrismaAdapter` is used to allow next-auth to use Prisma as the database. This way, next-auth can store user data in Prisma. Make sure that [[2024-10-16_Must-have-models-for-next-auth|some necessary tables for next-auth]] have been defined in the prisma.schema, and that the [[2024-10-13_Create-prisma-client-to-access-database|Prisma client]] `db` has been initialized.

If you haven't set up a Github OAuth app and obtained a client id and secret yet, please refer to [[2024-10-16_Create-Github-OAth-app|create github OAth app]]

Now we still lack of way to let Github to communicate with our Nextjs app, we will [[2024-10-16_Create-route-for-communating-with-Github|create a route]]  for that.
```bash
