# Transitions and Animations

Svelte provides powerful and easy-to-use mechanisms for adding transitions and animations to your components. These features can greatly enhance the user experience by making your application feel more responsive and engaging. Transitions are typically used when elements enter or leave the DOM, while animations offer more fine-grained control over how properties change over time. Svelte's approach is declarative, meaning you describe *what* you want to happen, and Svelte takes care of *how* to make it happen efficiently.

## Transitions

Transitions in Svelte are triggered when a component or element is added to or removed from the DOM. Svelte offers built-in transition directives that simplify common transition effects.

### Built-in Transitions

Svelte provides several built-in transition functions:

*   `fade`: Fades the element in or out.
*   `fly`: Flies the element in or out, originating from a specified position.
*   `slide`: Slides the element in or out vertically.
*   `scale`: Scales the element in or out.
*   `draw`: Animates the stroke of an SVG shape.

These transitions are easy to apply using directives. For example, to fade in an element:

```svelte
<script>
  let visible = true;
  import { fade } from 'svelte/transition';
</script>

<label>
  <input type="checkbox" bind:checked={visible}>
  Visible
</label>

{#if visible}
  <div transition:fade>
    This element will fade in and out.
  </div>
{/if}
```

The `transition:fade` directive applies the fade transition when the `div` element enters or leaves the DOM based on the `visible` variable.

You can customize the transition by passing parameters:

```svelte
<script>
  let visible = true;
  import { fly } from 'svelte/transition';
</script>

<label>
  <input type="checkbox" bind:checked={visible}>
  Visible
</label>

{#if visible}
  <div transition:fly={{ x: 200, duration: 500 }}>
    This element will fly in from the right.
  </div>
{/if}
```

In this example, the `fly` transition is customized with `x` (horizontal distance), and `duration` properties. The element will fly in from 200 pixels to the right over 500 milliseconds.

### Transition Parameters

Most transitions accept parameters to customize their behavior. Common parameters include:

*   `delay`: The number of milliseconds to wait before starting the transition.
*   `duration`: The number of milliseconds the transition should take.
*   `easing`: A function that controls the speed of the transition over time (e.g., `linear`, `ease-in-out`, or a custom cubic bezier function).

Refer to the Svelte documentation for specific parameters available for each transition.

### Transition Modifiers

Svelte provides transition modifiers that allow you to control when the transition occurs:

*   `in`: Applies the transition only when the element enters the DOM.
*   `out`: Applies the transition only when the element leaves the DOM.

For example:

```svelte
<script>
  let visible = true;
  import { fade } from 'svelte/transition';
</script>

<label>
  <input type="checkbox" bind:checked={visible}>
  Visible
</label>

{#if visible}
  <div transition:fade|in>
    This element will only fade in.
  </div>
{/if}
```

In this case, the `fade` transition will only be applied when the element enters the DOM.  It will disappear instantly when `visible` becomes `false`.

## Animations

Animations in Svelte provide more granular control over property changes over time. Unlike transitions, animations are not tied to elements entering or leaving the DOM. Instead, they can be triggered by any event or condition.

### The `animate` Directive

The `animate` directive is used to create animations. It requires a key, which is typically a string, and an object containing the animation parameters.

```svelte
<script>
  import { spring } from 'svelte/motion';

  let x = spring(0, {
    stiffness: 0.2,
    damping: 0.4
  });

  function handleClick() {
    $x = Math.random() * 200;
  }
</script>

<button on:click={handleClick}>Move</button>

<div style="position: relative; width: 100px; height: 100px; background-color: red; transform: translateX({$x}px);"></div>
```

In this example, `svelte/motion`'s `spring` is imported, which creates a smoothly animating, spring-like value. Clicking the button updates the `$x` value, causing the `div` to animate to the new position.

### Animation Parameters

Key animation parameters include:

*   `from`: The starting value of the property.
*   `to`: The ending value of the property.
*   `duration`: The duration of the animation in milliseconds.
*   `easing`: An easing function to control the animation's speed.

### Using `tweened`

The `tweened` store from `svelte/motion` simplifies creating animations between values. It automatically interpolates between the current value and the target value, providing smooth transitions.

```svelte
<script>
  import { tweened } from 'svelte/motion';
  import { cubicOut } from 'svelte/easing';

  const progress = tweened(0, {
    duration: 400,
    easing: cubicOut
  });

  function handleClick() {
    $progress = 1;
  }
</script>

<button on:click={handleClick}>Animate</button>

<progress value={$progress}></progress>
```

Clicking the button sets `$progress` to 1, animating the progress bar from 0 to 1 using the `cubicOut` easing function.

## Common Challenges and Solutions

*   **Transitions not working:** Ensure the element is truly entering or leaving the DOM. Check for typos in the transition name or parameters.

*   **Animations feeling jerky:** Adjust the `stiffness` and `damping` parameters in spring animations, or experiment with different easing functions.  Longer `duration` values can also help smooth animations.

*   **Performance issues with complex animations:** Simplify animations, reduce the number of animated elements, or use CSS transforms instead of animating layout properties (e.g., `top`, `left`).

*   **Unexpected behavior with conditional transitions:**  Be mindful of the order of operations. Sometimes, combining `in` and `out` modifiers can lead to unexpected results if the element is being both added and removed in the same update cycle.

## Accessibility Considerations

When using transitions and animations, it's important to consider accessibility. Avoid excessive or distracting animations that can cause discomfort or seizures. Provide mechanisms for users to disable animations if they prefer.  Use the `prefers-reduced-motion` media query to detect if the user has requested reduced motion in their system settings.

```svelte
<svelte:window bind:matchMedia={'prefers-reduced-motion: reduce'} let:matches>
  {#if matches}
    <p>Animations are disabled.</p>
  {/if}
</svelte:window>
```

## External Resources

*   **Svelte Tutorial - Transitions:** [https://svelte.dev/tutorial/transition](https://svelte.dev/tutorial/transition)
*   **Svelte Tutorial - Animations:** [https://svelte.dev/tutorial/animate](https://svelte.dev/tutorial/animate)
*   **MDN Web Docs - prefers-reduced-motion:** [https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-reduced-motion](https://developer.mozilla.org/en-US/docs/Web/CSS/@media/prefers-reduced-motion)

## Summary

Transitions and animations are powerful tools for enhancing the user experience in Svelte applications. Transitions provide simple ways to animate elements entering and leaving the DOM, while animations offer more fine-grained control over property changes. By understanding the built-in transitions, the `animate` directive, and the `tweened` store, you can create engaging and responsive user interfaces. Remember to consider accessibility and performance when adding animations to your applications. Now, experiment with different transitions and animations to see how they can improve your Svelte projects. Try combining different easing functions and parameters to achieve unique and compelling effects.