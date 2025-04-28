---
date: 2025-03-22
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# Web Game Development 1: Love.js Setup and Installation

Love2d does not have official support for a web version, but there is a Github project [love.js](https://github.com/TannerRogalsky/love.js) that can convert Love2d games into web versions.

```sh
 git clone git@github.com:TannerRogalsky/love.js.git # Clone the love.js repository
 cd love.js/
 git submodule update --init --recursive # Update the submodules
```

Now the love.js repository is cloned and the submodules are updated. However, there are some things to keep in mind when using love.js:

1. The love.js depends on Python 2.7 to work.
2. The love.js only supports Love2d version 0.10.0.

So the game you have recently made may not be able to be converted into a web version using love.js. For example, in Love2d version 0.10.0, color settings are using numbers between 0-255, while in version 11.0.0 and later, they are using numbers between 0-1. Some adjustments will be needed in certain areas.
