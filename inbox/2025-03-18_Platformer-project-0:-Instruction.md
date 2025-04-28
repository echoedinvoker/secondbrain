---
date: 2025-03-18
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# Platformer project 0: Instruction

This is a side-scrolling game where you can use the arrow keys on the keyboard to control the character's movement, and you can use the space bar to make the character jump. In the game, you need to avoid obstacles and enemies to reach the destination.

Because we need to create jumping actions, we need to simulate the physics rules of the real world. In Love2D, we can use `love.physics` to simulate physics rules. You can refer to the official documentation: [love.physics](https://love2d.org/wiki/love.physics)

You will find that the content of `love.physics` is somewhat complex, in fact it requires some learning curve to master. However, there is an open source project on Github that allows us to use the love2d physics engine more easily, the link is: [Windfield](https://github.com/a327ex/windfield), we can clone this project as a library to use.

Let's create a new project and clone the Windfield project as a library:

```bash
 mkdir platformer
 cd platformer/
 touch main.py
 mkdir libraries
 cd libraries/
 git clone git@github.com:a327ex/windfield.git
 cd ..
 tree
.
├── libraries
│   └── windfield
│       ├── README.md
│       └── windfield
│           ├── init.lua
│           └── mlib
│               ├── Changes.txt
│               ├── LICENSE.md
│               ├── mlib.lua
│               └── README.md
└── main.py
```
