# Transitions and Animations

Svelte offers a powerful and easy-to-use system for adding transitions and animations to your web applications. These visual effects can greatly enhance the user experience, making your application feel more polished and responsive. This module will guide you through the fundamentals of Svelte's transition and animation directives, enabling you to create compelling and dynamic interfaces.

Svelte's approach to transitions and animations is declarative. You specify *how* elements should animate when they enter or leave the DOM, and Svelte handles the complex calculations and rendering behind the scenes. This simplifies the process compared to manipulating CSS classes or using JavaScript-based animation libraries directly.

## Transitions

Transitions in Svelte are triggered when an element is added to (entering) or removed from (leaving) the DOM. Svelte provides several built-in transition directives, and you can also create your own custom transitions.

### Built-in Transitions

Svelte offers a set of built-in transition directives that cover common animation effects. These directives are easy to use and provide a good starting point for most transition needs. The built-in transitions are:

*   `fade`: Fades an element in or out.
*   `fly`: Moves an element in or out from a specified direction.
*   `slide`: Slides an element in or out vertically.
*   `scale`: Scales an element in or out.
*   `blur`: Blurs an element in or out.

To apply a transition, you use the `transition:` directive followed by the name of the transition. For example, to fade an element in and out, you would use `transition:fade`.

```svelte
<script>
  let visible = false;
</script>

<button on:click={() => visible = !visible}>
  Toggle
</button>

{#if visible}
  <div transition:fade>
    This element will fade in and out.
  </div>
{/if}
```

This code snippet demonstrates a simple toggle button that controls the visibility of a `div` element. When the `visible` variable is true, the `div` is rendered, and the `transition:fade` directive causes it to fade in. When `visible` is set to false, the `div` is removed from the DOM, and it fades out.

### Transition Parameters

Most transitions accept parameters to customize their behavior. These parameters are passed as an object to the transition directive.  Common parameters include `duration`, `delay`, and `easing`.

*   `duration`: Specifies the length of the transition in milliseconds. The default duration varies depending on the transition.
*   `delay`: Specifies the amount of time to wait before starting the transition, in milliseconds.
*   `easing`: Specifies the easing function to use for the transition. Easing functions control the rate of change of the animation over time. Svelte uses the [Robert Penner easing functions](http://robertpenner.com/easing/) which are also commonly used in CSS and other animation libraries.

Here's an example of using parameters with the `fly` transition:

```svelte
<script>
  let visible = false;
</script>

<button on:click={() => visible = !visible}>
  Toggle
</button>

{#if visible}
  <div transition:fly={{ x: 200, y: 0, duration: 500, delay: 250, easing: x => x }}>
    This element will fly in from the right.
  </div>
{/if}
```

In this example, the `fly` transition is configured to move the element in from the right (`x: 200`), with a duration of 500 milliseconds, a delay of 250 milliseconds, and a linear easing function.

### `in:` and `out:` Directives

Sometimes you might want to apply different transitions for when an element enters and leaves the DOM.  Svelte provides the `in:` and `out:` directives for this purpose.

```svelte
<script>
  let visible = false;
</script>

<button on:click={() => visible = !visible}>
  Toggle
</button>

{#if visible}
  <div in:fade out:slide>
    This element will fade in and slide out.
  </div>
{/if}
```

In this example, the element will fade in when it is added to the DOM, and it will slide out when it is removed. You can also pass parameters to the `in:` and `out:` directives:

```svelte
<div in:fade={{ duration: 2000 }} out:slide={{ duration: 500 }}>
  This element will fade in slowly and slide out quickly.
</div>
```

### Custom Transitions

While Svelte's built-in transitions are useful, you may need more control over the animation. Svelte allows you to define your own custom transitions using JavaScript functions.

A custom transition function takes two arguments: the DOM element being transitioned and an optional parameter object. It should return an object that describes the transition. This object must have a `duration` property (in milliseconds) and can optionally have a `tick` property. The `tick` property is a function that is called repeatedly during the transition, allowing you to update the element's styles.

Here's an example of a custom transition that changes the background color of an element:

```svelte
<script>
  import { tick } from 'svelte';

  function colorChange(node, { startColor, endColor, duration }) {
    return {
      duration,
      tick: (t) => {
        const r = Math.round(startColor.r + (endColor.r - startColor.r) * t);
        const g = Math.round(startColor.g + (endColor.g - startColor.g) * t);
        const b = Math.round(startColor.b + (endColor.b - startColor.b) * t);
        node.style.backgroundColor = `rgb(${r}, ${g}, ${b})`;
      }
    };
  }

  let visible = false;
</script>

<button on:click={() => visible = !visible}>
  Toggle
</button>

{#if visible}
  <div transition:colorChange={{ startColor: { r: 255, g: 0, b: 0 }, endColor: { r: 0, g: 0, b: 255 }, duration: 1000 }}>
    This element will transition from red to blue.
  </div>
{/if}
```

This example defines a `colorChange` transition that smoothly changes the background color of an element from a `startColor` to an `endColor` over a specified `duration`.  The `tick` function calculates the intermediate color values based on the transition progress `t` (which ranges from 0 to 1).

## Animations

Animations in Svelte are different from transitions. Animations are not triggered by elements entering or leaving the DOM. Instead, they run continuously or are triggered by specific events. Svelte provides the `animate:` directive for creating animations.

### `animate:` Directive

The `animate:` directive allows you to animate between different states of an element. It requires a key that identifies the element. When the element's state changes, Svelte will smoothly animate the changes.

The `animate:` directive takes two arguments: the key and an optional parameter object. The key should be a unique identifier for the element. The parameter object can include properties like `duration`, `delay`, and `easing`, similar to transitions.

```svelte
<script>
  let width = 100;
</script>

<input type="range" bind:value={width} min="50" max="300">

<div animate:size={{ duration: 500 }}>
  This element's width will animate when the range input changes.
</div>

<style>
  div {
    width: {width}px;
    height: 50px;
    background-color: lightblue;
  }
</style>
```

In this example, the `animate:size` directive animates the width of the `div` element whenever the `width` variable changes. The `size` key is used to identify the element.  The animation will run for 500 milliseconds.

### Custom Animation Attributes

Svelte animations can animate any numeric attribute. In the above example, we created a custom animation attribute named `size`. This is not required and is purely for readability. One could have used a built in attribute, such as `animate:width`, but this has the downside of potentially clashing with the element's style declaration.

```svelte
<script>
  let width = 100;
</script>

<input type="range" bind:value={width} min="50" max="300">

<div style="width: {width}px; height: 50px; background-color: lightblue;" animate:width={{ duration: 500 }}>
  This element's width will animate when the range input changes.
</div>
```

This example is equivalent to the previous one. However, we are now animating the built-in `width` attribute. This will work, but it is generally recommended to use custom animation attributes to avoid potential conflicts with other styles.

### Using Stores with Animations

Svelte stores are a powerful way to manage application state. You can combine stores with animations to create dynamic and reactive interfaces.

```svelte
<script>
  import { writable } from 'svelte/store';

  const count = writable(0);

  function increment() {
    count.update(n => n + 1);
  }
</script>

<button on:click={increment}>Increment</button>

<div animate:count={{ duration: 200 }}>
  Count: {$count}
</div>
```

In this example, the `count` store is used to track a numerical value.  The `animate:count` directive animates the `div` element's content whenever the `count` store changes. This will smoothly update the displayed count value.  Note that this animation will not be visible to the user, since the text is updated as soon as the value in the store changes. This is purely an illustrative example.

## Common Challenges and Solutions

*   **Transitions not working:** Ensure that the element is actually being added to or removed from the DOM. Check for typos in the transition directive name.  Verify that the `visible` condition is correctly toggling.
*   **Animations not smooth:** Experiment with different easing functions to find one that provides the desired effect. Adjust the `duration` to control the animation speed.
*   **Performance issues:** Complex transitions and animations can impact performance, especially on older devices.  Minimize the number of animated elements. Use CSS transforms instead of layout-changing properties like `width` and `height` where possible. Consider using the `requestAnimationFrame` API directly for fine-grained control.
*   **Combining transitions and animations:** Be mindful of how transitions and animations interact.  They can sometimes interfere with each other, leading to unexpected results.  Test thoroughly and adjust parameters as needed.

## External Resources

*   [Svelte Tutorial - Transitions](https://svelte.dev/tutorial/transition)
*   [Svelte Tutorial - Animations](https://svelte.dev/tutorial/animate)
*   [Robert Penner Easing Functions](http://robertpenner.com/easing/)

## Thoughtful Engagement

Consider the following questions to deepen your understanding of transitions and animations in Svelte:

*   How can transitions be used to improve the perceived performance of a loading state?
*   What are some creative ways to use custom transitions to create unique user experiences?
*   How can animations be used to provide feedback to user interactions?
*   What are the accessibility considerations when using transitions and animations?

## Summary

Svelte's transition and animation directives provide a convenient and powerful way to add visual effects to your web applications. Transitions are triggered when elements enter or leave the DOM, while animations run continuously or are triggered by specific events. By understanding the built-in transitions, custom transitions, and the `animate:` directive, you can create compelling and dynamic user interfaces that enhance the overall user experience. Remember to optimize your animations for performance and consider accessibility best practices.