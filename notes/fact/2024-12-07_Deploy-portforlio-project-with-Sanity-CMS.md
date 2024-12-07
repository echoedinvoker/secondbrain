---
date: 2024-12-07
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Deploy portforlio project with Sanity CMS

We will deploy not only portfolio projects, but also Sanity CMS studio.

## Deploy the project

```bash
git init
git branch -M main
git git add .
git commit -m "Initial commit"

# create a new repository on github

git remote add origin git@github.com:echoedinvoker/sv5-portfolio-course.git
git push -u origin main

# go to vercel.com to create a new project and deploy the project
# don't forget to copy .env file content and paste to vercel environment variables

```

Add the url of the deployed project to the studio's cors origins, so that the studio can accept requests from the deployed project.

![add-origin-to-sanity.png](../assets/imgs/add-origin-to-sanity.png)

## Deploy the studio

Next, we need to deploy the Sanity Studio CMS to sanity.studio also, so that users can manage the content of the project.

```bash
# go to Sanity Studio directory
sanity deploy

# ...

Success! Studio deployed to https://dev-portfolio-course.sanity.studio/
#                           ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ this is the url of the deployed studio

```








