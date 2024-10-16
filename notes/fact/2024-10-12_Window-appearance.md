---
date: 2024-10-12
type: fact
aliases:
  -
hubs:
  - "[[Godot]]"
---

# Window appearance

The appearance of the window is determined by [[2024-10-12_2D-Viewport|the viewport]].

- Viewport can be dynamically resized and moved.
  - In some side-scrolling games, the viewport may need to move along with the player's movement.
    ![moving-viewport.png](../assets/imgs/moving-viewport.png)

- The size of the viewport is the size of the window.
  - In some devices, the window size is dynamic changing. It will let the viewport size grow or shrink, but not stretch.


**Root object can only render ONE scene**

![render-scene.png](../assets/imgs/render-scene.png)

But this does not mean that there can only be one scene under root, there can also be other scenes, but only one scene will be rendered. We may switch the scene that needs to be rendered during the game process. Or some scenes are used for calculations or storing data and do not need to be rendered.

![render-scene2.png](../assets/imgs/render-scene2.png)

