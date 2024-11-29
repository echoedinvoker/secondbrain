---
date: 2024-11-25
type: fact
aliases:
  -
hubs:
  - "[[sv5]]"
---

# Create sanity project locally

In [[2024-11-25_Create-structure-of-work-experience-(2nd-portion-of-about-section)|this topic]], we used dummy data to create the structure of the work experience section. But we plan to use headless CMS to manage the data. Below is the process to create a sanity project locally:

```bash
~/D/g/s/sv5 > npm create sanity@latest -- --project sccxiprj --dataset production --template clean --typescript --output-path studio-dev-portfolio-course
#             ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
#             copy paste from project page on sanity.io (it means you need to be logged in to sanity.io and have a project created)
#             `studio-dev-portfolio-course` is project(folder) name, better separate from the main project.
Need to install the following packages:
create-sanity@3.64.3
Ok to proceed? (y) 

npm warn deprecated inflight@1.0.6: This module is not supported, and leaks memory. Do not use it. Check out lru-cache if you want a good and tested way to coalesce async requests by a key value, which is much more comprehensive and powerful.
npm warn deprecated glob@7.2.3: Glob versions prior to v9 are no longer supported

> npx
> create-sanity --project sccxiprj --dataset production --template clean --typescript --output-path studio-dev-portfolio-course


   ╭─────────────────────────────────────────────────────────────────────────────╮
   │                                                                             │
   │   The Sanity CLI now collects telemetry data on general usage and errors.   │
   │   This helps us improve Sanity and prioritize features.                     │
   │                                                                             │
   │   To opt in/out, run npx sanity telemetry enable/disable.                   │
   │                                                                             │
   │   Learn more here:                                                          │
   │   https://www.sanity.io/telemetry                                           │
   │                                                                             │
   ╰─────────────────────────────────────────────────────────────────────────────╯

You're setting up a new project!
We'll make sure you have an account with Sanity.io. Then we'll
install an open-source JS content editor that connects to
the real-time hosted API on Sanity.io. Hang on.

Press ctrl + C at any time to quit.

Prefer web interfaces to terminals?
You can also set up best practice Sanity projects with
your favorite frontends on https://www.sanity.io/templates

We can't find any auth credentials in your Sanity config
- log in or create a new account

? Login type GitHub
#            ^^^^^^ choose login type, I choose GitHub 

Opening browser at https://api.sanity.io/v1/auth/login/github?type=token&label=matt+%2F+Linux&origin=http%3A%2F%2Flocalhost%3A4321%2Fcallback

⠹ Waiting for browser login to complete... # this will open a browser window to authenticate with GitHub

Good stuff, you're now authenticated. You'll need a project to keep your
datasets and collaborators safe and snug.
✅ Fetching existing projects
✅ Bootstrapping files from template
✅ Resolving latest module versions
✅ Creating default project files
? Package manager to use for installing dependencies? npm
#                                                     ^^^ choose package manager, I choose npm
Running 'npm install --legacy-peer-deps'
npm warn deprecated inflight@1.0.6: This module is not supported, and leaks memory. Do not use it. Check out lru-cache if you want a good and tested way to coalesce async requests by a key value, which is much more comprehensive and powerful.
npm warn deprecated @humanwhocodes/config-array@0.13.0: Use @eslint/config-array instead
npm warn deprecated rimraf@3.0.2: Rimraf versions prior to v4 are no longer supported
npm warn deprecated glob@7.2.3: Glob versions prior to v9 are no longer supported
npm warn deprecated @humanwhocodes/object-schema@2.0.3: Use @eslint/object-schema instead
npm warn deprecated eslint@8.57.1: This version is no longer supported. Please see https://eslint.org/version-support for other options.

added 1048 packages, and audited 1049 packages in 1m

206 packages are looking for funding
  run `npm fund` for details

found 0 vulnerabilities

Success! Now, use these commands to continue:

First: cd /home/mattc/Documents/gith/std/sv5/studio-dev-portfolio-course - to enter project’s directory
Then: npm run dev - to run Sanity Studio

Other helpful commands
sanity docs - to open the documentation in a browser
sanity manage - to open the project settings in a browser
sanity help - to explore the CLI manual

# Sanity Studio is now installed in `studio-dev-portfolio-course` folder

~/D/g/s/sv5 > cd studio-dev-portfolio-course/  # enter the sanity project folder

~/D/g/s/s/studio-dev-portfolio-course > npm run dev  # run the sanity studio

```
```
```

