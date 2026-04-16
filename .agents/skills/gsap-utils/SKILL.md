---
name: gsap-utils
description: Official GSAP skill for gsap.utils — clamp, mapRange, normalize, interpolate, random, snap, toArray, wrap, pipe. Use when the user asks about gsap.utils, clamp, mapRange, random, snap, toArray, wrap, or helper utilities in GSAP.
license: MIT
---

# gsap.utils

## When to Use This Skill

Apply when writing or reviewing code that uses **gsap.utils** for math, array/collection handling, unit parsing, or value mapping in animations (e.g. mapping scroll to a value, randomizing, snapping to a grid, or normalizing inputs).

**Related skills:** Use with **gsap-core**, **gsap-timeline**, and **gsap-scrolltrigger** when building animations.

## Overview

**gsap.utils** provides pure helpers; no need to register. All are on **gsap.utils** (e.g. `gsap.utils.clamp()`).

**Omitting the value: function form.** Many utils accept the value as the **last** argument. If you omit that argument, the util returns a **function** that accepts the value later. **Exception: random()** — pass **true** as the last argument to get a reusable function.

```javascript
// With value: returns the result
gsap.utils.clamp(0, 100, 150); // 100

// Without value: returns a function
let c = gsap.utils.clamp(0, 100);
c(150);  // 100
c(-10);  // 0
```

## Clamping and Ranges

### clamp(min, max, value?)

```javascript
gsap.utils.clamp(0, 100, 150); // 100
let clampFn = gsap.utils.clamp(0, 100);
clampFn(150); // 100
```

### mapRange(inMin, inMax, outMin, outMax, value?)

Maps a value from one range to another:

```javascript
gsap.utils.mapRange(0, 100, 0, 500, 50);  // 250
gsap.utils.mapRange(0, 1, 0, 360, 0.5);   // 180

let mapFn = gsap.utils.mapRange(0, 100, 0, 500);
mapFn(50);  // 250
```

### normalize(min, max, value?)

Returns a value normalized to 0–1:

```javascript
gsap.utils.normalize(0, 100, 50);   // 0.5
let normFn = gsap.utils.normalize(0, 100);
normFn(50); // 0.5
```

### interpolate(start, end, progress?)

Interpolates between two values. Handles numbers, colors, and objects:

```javascript
gsap.utils.interpolate(0, 100, 0.5);             // 50
gsap.utils.interpolate("#ff0000", "#0000ff", 0.5); // mid color
gsap.utils.interpolate({ x: 0, y: 0 }, { x: 100, y: 50 }, 0.5); // { x: 50, y: 25 }

let lerp = gsap.utils.interpolate(0, 100);
lerp(0.5); // 50
```

## Random and Snap

### random(minimum, maximum[, snapIncrement, returnFunction]) / random(array[, returnFunction])

Pass **true** as the last argument to get a reusable function:

```javascript
gsap.utils.random(-100, 100);           // e.g. 42.7
gsap.utils.random(0, 500, 5);           // 0–500, snapped to nearest 5

let randomFn = gsap.utils.random(-200, 500, 10, true);
randomFn();  // random value, snapped to 10

// Array: pick one value at random
gsap.utils.random(["red", "blue", "green"]);
let randomFromArray = gsap.utils.random([0, 100, 200], true);
```

**String form in tween vars:**

```javascript
gsap.to(".box", { x: "random(-100, 100, 5)", duration: 1 });
gsap.to(".item", { backgroundColor: "random([red, blue, green])" });
```

### snap(snapTo, value?)

Snaps to the nearest multiple or nearest value in an array:

```javascript
gsap.utils.snap(10, 23);     // 20
gsap.utils.snap([0, 100, 200], 150); // 100 or 200 (nearest)

let snapFn = gsap.utils.snap(10);
snapFn(23); // 20
```

### shuffle(array)

Returns a new array with elements in random order:

```javascript
gsap.utils.shuffle([1, 2, 3, 4]); // e.g. [3, 1, 4, 2]
```

### distribute(config)

Returns a function that assigns a value to each target based on position in the array or grid:

```javascript
gsap.to(".class", {
  scale: gsap.utils.distribute({
    base: 0.5,
    amount: 2.5,
    from: "center"
  })
});
```

**Config:**

| Property | Type | Description |
|----------|------|-------------|
| `base` | Number | Starting value. Default `0`. |
| `amount` | Number | Total to distribute across all targets. |
| `each` | Number | Amount to add between each target. |
| `from` | Number \| String \| Array | Where distribution starts: index, or `"start"`, `"center"`, `"edges"`, `"random"`, `"end"`. |
| `grid` | String \| Array | `[rows, columns]` or `"auto"` for grid position. |
| `axis` | String | For grid: limit to `"x"` or `"y"`. |
| `ease` | Ease | Distribute values along an ease curve. |

## Units and Parsing

### getUnit(value)

```javascript
gsap.utils.getUnit("100px");   // "px"
gsap.utils.getUnit("50%");     // "%"
gsap.utils.getUnit(42);        // "" (unitless)
```

### unitize(value, unit)

```javascript
gsap.utils.unitize(100, "px");  // "100px"
gsap.utils.unitize("2rem", "px"); // "2rem" (unchanged)
```

### splitColor(color, returnHSL?)

Converts a color string into `[red, green, blue]` or `[hue, saturation, lightness]`:

```javascript
gsap.utils.splitColor("red");                    // [255, 0, 0]
gsap.utils.splitColor("#6fb936");                // [111, 185, 54]
gsap.utils.splitColor("rgba(204, 153, 51, 0.5)"); // [204, 153, 51, 0.5]
gsap.utils.splitColor("#6fb936", true);          // [94, 55, 47] (HSL)
```

## Arrays and Collections

### selector(scope)

Returns a scoped selector function that finds elements only within the given element or ref:

```javascript
const q = gsap.utils.selector(containerRef);
q(".box");  // array of .box elements inside container
gsap.to(q(".circle"), { x: 100 });
```

### toArray(value, scope?)

Converts a value to an array (selector string, NodeList, single element, or array):

```javascript
gsap.utils.toArray(".item");           // array of elements
gsap.utils.toArray(".item", container); // scoped to container
```

### pipe(...functions)

Composes functions left-to-right:

```javascript
const fn = gsap.utils.pipe(
  (v) => gsap.utils.normalize(0, 100, v),
  (v) => gsap.utils.snap(0.1, v)
);
fn(50); // normalized then snapped
```

### wrap(min, max, value?)

Wraps a value into the range min–max (cyclic):

```javascript
gsap.utils.wrap(0, 360, 370);  // 10
gsap.utils.wrap(0, 360, -10);   // 350
```

### wrapYoyo(min, max, value?)

Wraps value with a yoyo (bounces at ends):

```javascript
gsap.utils.wrapYoyo(0, 100, 150); // 50 (bounces back)
```

## Best practices

- ✅ Omit the value argument to get a reusable function when the same range/config is used many times.
- ✅ Use **snap** for grid-aligned or step-based values; use **toArray** when a real array is needed.
- ✅ Use **gsap.utils.selector(scope)** in components so selectors are scoped to a container or ref.

## Do Not

- ❌ Assume **mapRange** / **normalize** handle units; they work on numbers.

### Learn More

https://gsap.com/docs/v3/GSAP/UtilityMethods/
