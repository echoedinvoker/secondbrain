---
date: 2025-03-22
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# Web Game Development 3: Convert love2d game to js by love.js

We have already set up love.js in [[2025-03-22_Web-Game-Development-1:-Love.js-Setup-and-Installation|Web Game Development 1: Love.js Setup and Installation]], and adjusted our game in [[2025-03-22_Web-Game-Development-2:-Check-if-our-game-works-on-love10|Web Game Development 2: Check if our game works on love10]]. Now we need to convert our game into a js version by love.js so that we can play our game on the web.

First, we need to copy our game to the love.js/debug folder.

```sh
cd love.js/debug/
cp -r /path/to/your/game topDownShooter # assume our game name is topDownShooter
```

Then we need to convert our game into a js version by love.js.

However, the conversion tool of love.js needs to run on Python 2.7. The easiest way is to use Docker.

```sh
 docker run -it --name love-js-container -v $(pwd):/work python:2.7 bash # create a container, mount the current directory to /work inside the container
                                                                          # so we should run this command in the directory where the love.js is installed
                                                                          # then enter the container with bash interactive mode
# if you don't have the image `python:2.7`, it will be downloaded automatically

# now you're already in the container
root@a35337f672f7:/# cd work/love.js/debug/ 
root@a35337f672f7:/work/love.js/debug# python ../emscripten/tools/file_packager.py game.data --preload topDownShooter@/ --js-output=game.js
#                                                                                  ^^^^^^^^^           ^^^^^^^^^^^^^^^^             ^^^^^^^ output js file
#                                                                                  ^^^^^^^^^           ^^^^^^^^^^^^^^^^ game folder and `@/` means the root of the game
#                                                                                  ^^^^^^^^^ output data file
# if there are some welcome messages, you need to run the same command again
root@a35337f672f7:/work/love.js/debug# python ../emscripten/tools/file_packager.py game.data --preload topDownShooter@/ --js-output=game.js
# no message means the conversion is successful
root@a35337f672f7:/work/love.js/debug# ls # check the files
   game.data  game.js  index.html  love.js  love.js.map  topDownShooter
#  ^^^^^^^^^^^^^^^^^^ these are the files we need to run our game on the web

root@a35337f672f7:/work/love.js/debug# exit # exit the container
```

Now, we've got the converted files, which are `game.data`, `game.js`, we can directly test if our game works on the web by python simple server.

```sh
python -m http.server 8000
```

Then open your browser and go to `http://localhost:8000`, you will see your game running on the website.

