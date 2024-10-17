---
date: 2024-10-16
type: fact
aliases:
  -
hubs:
  - "[[Next]]"
---

# Recommended Initial Design

Due to the existence of Next.js's [[2024-10-15_The-Full-Route-Cache-System|Full Route Cache]] mechanism, it is best for us to design our application using the steps listed below before starting implementation. This can help us better understand the structure of our application and make more effective use of Next.js's Fall route caching mechanism.

1. Identify all the different routes you want your app to have + the data that each shows [[2024-10-16_Example-of-identifying-all-routes-table|example]]

2. Make [[2024-10-16_Path-Helpers|path helpers]] functions

3. Create your routing folders + page.tsx files based on step #1

4. Identify the places where data changes in your app

5. Make empty server actions for each of those

6. Add in comments on what paths you'll need to revalidate for each server action. [[2024-10-16_Adding-comments-on-what-paths-you'll-need-to-revalidate-for-each-server-action|example]]
