---
name: gsap-frameworks
description: Official GSAP skill for Vue, Svelte, and other frameworks — lifecycle, when to create/kill tweens and ScrollTriggers, scoping selectors, cleanup on unmount. Use when the user wants GSAP in Vue, Nuxt, Svelte, or SvelteKit.
license: MIT
---

# GSAP with Vue, Svelte, and Other Frameworks

## When to Use This Skill

Apply when writing or reviewing GSAP code in Vue (or Nuxt), Svelte (or SvelteKit), or other component frameworks that use a lifecycle (mounted/unmounted). For **React** specifically, use **gsap-react** (useGSAP hook, gsap.context()).

**Related skills:** For tweens and timelines use **gsap-core** and **gsap-timeline**; for scroll-based animation use **gsap-scrolltrigger**; for React use **gsap-react**.

## Principles (All Frameworks)

- **Create** tweens and ScrollTriggers **after** the component's DOM is available (e.g. onMounted, onMount).
- **Kill or revert** them in the **unmount** cleanup so nothing runs on detached nodes.
- **Scope selectors** to the component root so `.box` only matches elements inside that component.

## Vue 3 (Composition API)

```javascript
import { onMounted, onUnmounted, ref } from "vue";
import { gsap } from "gsap";
import { ScrollTrigger } from "gsap/ScrollTrigger";
gsap.registerPlugin(ScrollTrigger); // once per app, e.g. in main.js

export default {
  setup() {
    const container = ref(null);
    let ctx;

    onMounted(() => {
      if (!container.value) return;
      ctx = gsap.context(() => {
        gsap.to(".box", { x: 100, duration: 0.6 });
        gsap.from(".item", { autoAlpha: 0, y: 20, stagger: 0.1 });
      }, container.value);
    });

    onUnmounted(() => {
      ctx?.revert();
    });

    return { container };
  }
};
```

## Vue 3 (script setup)

```html
<script setup>
import { onMounted, onUnmounted, ref } from "vue";
import { gsap } from "gsap";

const container = ref(null);
let ctx;

onMounted(() => {
  if (!container.value) return;
  ctx = gsap.context(() => {
    gsap.to(".box", { x: 100 });
    gsap.from(".item", { autoAlpha: 0, stagger: 0.1 });
  }, container.value);
});

onUnmounted(() => {
  ctx?.revert();
});
</script>

<template>
  <div ref="container">
    <div class="box">Box</div>
    <div class="item">Item</div>
  </div>
</template>
```

## Svelte

Use **onMount** return function for cleanup:

```html
<script>
  import { onMount } from "svelte";
  import { gsap } from "gsap";

  let container;

  onMount(() => {
    if (!container) return;
    const ctx = gsap.context(() => {
      gsap.to(".box", { x: 100 });
      gsap.from(".item", { autoAlpha: 0, stagger: 0.1 });
    }, container);
    return () => ctx.revert();
  });
</script>

<div bind:this={container}>
  <div class="box">Box</div>
  <div class="item">Item</div>
</div>
```

## Scoping Selectors

Always pass the **scope** (container element or ref) as the second argument to **gsap.context(callback, scope)**:

- ✅ **gsap.context(() => { gsap.to(".box", ...) }, containerRef)** — `.box` is only searched inside `containerRef`.
- ❌ Running **gsap.to(".box", ...)** without a context scope can affect other instances or the rest of the page.

## ScrollTrigger Cleanup

ScrollTrigger instances created inside **gsap.context()** are reverted when you call **ctx.revert()**. Call **ScrollTrigger.refresh()** after layout changes (e.g. after nextTick in Vue, tick in Svelte, or after async content load).

## When to Create vs Kill

| Lifecycle | Action |
|-----------|--------|
| **Mounted** | Create tweens and ScrollTriggers inside **gsap.context(scope)**. |
| **Unmount / Destroy** | Call **ctx.revert()** so all animations and ScrollTriggers are killed. |

## Do Not

- ❌ Create tweens or ScrollTriggers before the component is mounted; DOM nodes may not exist yet.
- ❌ Use selector strings without a **scope**; pass the container to gsap.context() as the second argument.
- ❌ Skip cleanup; always call **ctx.revert()** in onUnmounted / onMount's return.
- ❌ Register plugins inside a component body that runs every render; register once at app level.

### Learn More

- **gsap-react** skill for React-specific patterns (useGSAP, contextSafe).
- https://gsap.com/docs/v3/GSAP/
