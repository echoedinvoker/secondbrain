---
date: 2024-11-08
type: fact
aliases:
  -
hubs:
  - "[[RUST]]"
---

# A JS demo of a nasty bug that Rust ownership want to avoid

```js
const engine = { working: true };

const mustang = {
  name: "Mustang",
  engine
}

const camaro = {
  name: "Camaro",
  engine
}

function stopEngine(car) {
  car.engine.working = false;
}
```

Then we execute above objects and function in the browser console:

```js
stopEngine(mustang);
// undefined

mustang
// {name: "Mustang", engine: {working: false}}
//                            ^^^^^^^^^^^^^^ expected: false, actual: false, Good!

camaro
// {name: "Camaro", engine: {working: false}}
//                           ^^^^^^^^^^^^^^ expected: true, actual: false, Bad!
//                                          It's not expected to change camaro's engine status.
//                                          So the bug is here.
```

![refer-to-same-obj.png](../assets/imgs/refer-to-same-obj.png)

That's because `mustang` and `camaro` are referring to the same `engine` object. So when we change `engine` object in `stopEngine` function, it affects both `mustang` and `camaro`.

The ownership concept in Rust is designed to avoid this kind of bug.
