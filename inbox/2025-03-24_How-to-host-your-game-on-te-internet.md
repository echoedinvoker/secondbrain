---
date: 2025-03-24
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# How to host your game on te internet

First, you need to find a hosting provider with a free plan.

After signing up and selecting the free plan, create a new website and find the place where you should upload your game.

Go to the love.js folder which you have converted from your game and copy the `game.data` and `game.js` files.
```sh
 tree
.
├── debug
│   ├── game.data # copy these 
│   ├── game.js # two files
│   ├── index.html
│   ├── love.js
│   ├── love.js.map
│   └── topDownShooter
├── emscripten
├── LICENSE
├── readme.md
├── release-compatibility
│   ├── index.html
│   ├── love.js
│   ├── love.js.mem
│   ├── game.data # paste to
│   ├── game.js # here
│   └── theme
├── release-performance
└── spec
```

Then upload all the files from the `release-compatibility` folder to the website of your hosting provider.

Now, your website should be live and you can share the link with your friends.

There are some other part out of game like game title, you can modify it in `index.html` file.

