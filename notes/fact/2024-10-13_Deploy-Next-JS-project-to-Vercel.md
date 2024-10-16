---
date: 2024-10-13
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Deploy Next JS project to Vercel

```bash
mattc@x ~/r/my-app (main)> npx vercel   # Run this command in the root of the project
? Set up and deploy “~/reposities/my-app”? yes
? Which scope do you want to deploy to? echoedinvoker\'s projects
? Link to existing project? no
? What’s your project’s name? my-app
? In which directory is your code located? ./  # This must be the root of the project
Local settings detected in vercel.json:
Auto-detected Project Settings (Next.js):
- Build Command: next build
- Development Command: next dev --port $PORT
- Install Command: `yarn install`, `pnpm install`, `npm install`, or `bun install`
- Output Directory: Next.js default
? Want to modify these settings? no
🔗  Linked to echoedinvokers-projects/my-app (created .vercel)
🔍  Inspect: https://vercel.com/echoedinvokers-projects/my-app/HBdjEZDaX7wn8BBrLLbWbW8BkVv4 [1s]    # Link to the project dashboard on Vercel
✅  Production: https://my-1wxtgkkdb-echoedinvokers-projects.vercel.app [1s]  # Link to the deployed project website, you can share this link with others
📝  Deployed to production. Run `vercel --prod` to overwrite later (https://vercel.link/2F).
💡  To change the domain or build command, go to https://vercel.com/echoedinvokers-projects/my-app/settings
```


