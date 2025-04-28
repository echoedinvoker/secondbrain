---
date: 2025-03-24
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# Generating the APK

## Add conf.lua to the game project
```lua
function love.conf(t)
    -- Actual values are not important, just the ratio
    -- it will determine which screen mode to use initially
    t.window.width = 720
    t.window.height = 960
    --                ^^^ because height is greater than width, it will use portrait mode when launching the game

    t.modules.joystick = false -- remove joystick module out of the love because it's a big module and no need in mobile games, so it will speed up the game launch time
    t.externalstorage = true -- saving data doesn't work in some devices, so this will make it work
end
```

## zip all the files of the game project and rename it to `game.love`

In the game project directory:

```sh
zip -r game.love * # zip all the files in the current directory
love game.love # `game.love` can be run by love2d, so you can test the game before building the APK
```

## Move `game.love` to the `love-android` project

```sh
mkdir ~/love-android/app/src/main/assets/ # create the `assets` directory if it doesn't exist
mv game.love ~/love-android/app/src/main/assets/ # move the `game.love` to the `assets` directory

```

## Edit `AndroidManifest.xml` in the `love-android` project

If necessary, you can modify the `AndroidManifest.xml` file to change some settings of the game, such as the name of the game, icon, etc.


## Build the APK

Use the following command to build the APK file:
```sh
./gradlew assembleNormal -Dorg.gradle.jvmargs="-Xmx4g -XX:MaxMetaspaceSize=512m"
#                        ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ if you have a low memory error issue, add this option to increase the memory limit
```
This command need Java and Android SDK, NDK to be installed on your computer. We've setup Android SDK and NDK in the previous steps but if you haven't installed Java, you can install it by running the following command:
```sh
sudo pacman -S jdk17-openjdk
sudo archlinux-java set java-17-openjdk
```

And set the `JAVA_HOME` and `PATH` environment variables in your shell configuration file (e.g. `~/.config/fish/config.fish`):
```fish
...
set -gx JAVA_HOME "/usr/lib/jvm/java-17-openjdk"
set -gx PATH "$JAVA_HOME/bin:$PATH"
...
```

If you're the first time building the APK, it will take a while to download the necessary dependencies. After that, the APK file will be generated in the `love-android/app/build/outputs/apk/normalRecord(or normalNoRecord)/debug/` directory.

