---
date: 2024-10-12
type: fact
aliases:
  -
hubs:
  - "[[Godot]]"
---

# Create a 2D thing with collision detect

**Create a collision area**

It need node `Area2D` and `CollisionShape2D` as child node.

![area2d-collision2d.png](../assets/imgs/area2d-collision2d.png)


**Visualize the collision area**

Because the `CollisionShape2D` is invisible, we can add a `Sprite2D` node to visualize it.

![sprite2d-for-area2d.png](../assets/imgs/sprite2d-for-area2d.png)


**Adjust the areas between `CollisionShape2D` and `Sprite2D`**

Usually, we need to set the areas of `Sprite2D` and `CollisionShape2D` to the same size and position.

![fit-the-areas-between-collistionshape2d-sprite2d.png](../assets/imgs/fit-the-areas-between-collistionshape2d-sprite2d.png)

If you want to adjust the areas more precisely, you can use the properties below:

![adjust-area-in-details.png](../assets/imgs/adjust-area-in-details.png)
