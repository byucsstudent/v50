# Svelte Components

Svelte is a component-based framework for building user interfaces. Components are the building blocks of any Svelte application, encapsulating HTML, CSS, and JavaScript into reusable units. Understanding how to create, use, and manage components is fundamental to mastering Svelte. This module will guide you through the process of creating and utilizing Svelte components effectively.

Svelte components are written in `.svelte` files. These files contain markup (HTML), styling (CSS), and behavior (JavaScript) all within a single file. The Svelte compiler then processes these files, transforming them into highly efficient JavaScript that updates the DOM when your application's state changes.

## Creating Your First Component

Let's start with a simple example. Create a new file named `Greeting.svelte` and add the following code:

```svelte
<script>
  let name = 'World';
</script>

<h1>Hello, {name}!</h1>

<style>
  h1 {
    color: blue;
  }
</style>
```

This component defines a variable `name` in the `<script>` section, displays a greeting using that variable in the HTML section, and styles the `<h1>` element in the `<style>` section.

## Using Components

Now, let's use our `Greeting` component in another component, say `App.svelte`:

```svelte
<script>
  import Greeting from './Greeting.svelte';
</script>

<main>
  <Greeting />
</main>

<style>
  main {
    text-align: center;
    padding: 1em;
  }
</style>
```

The `import` statement brings the `Greeting` component into `App.svelte`, and then the `<Greeting />` tag renders the component in the HTML.  This demonstrates the basic principle of component composition: building complex UIs by combining smaller, reusable components.

## Component Props

Props are how you pass data from a parent component to a child component. Let's modify our `Greeting` component to accept a `name` prop:

```svelte
<script>
  export let name;
</script>

<h1>Hello, {name}!</h1>

<style>
  h1 {
    color: blue;
  }
</style>
```

Notice the `export let name`.  The `export` keyword makes `name` a prop. Now, in `App.svelte`, we can pass a value for the `name` prop:

```svelte
<script>
  import Greeting from './Greeting.svelte';
</script>

<main>
  <Greeting name="Alice" />
  <Greeting name="Bob" />
</main>

<style>
  main {
    text-align: center;
    padding: 1em;
  }
</style>
```

This will render two greetings: "Hello, Alice!" and "Hello, Bob!".  Props allow you to customize the behavior and appearance of components based on the data you provide.

## Component Events

Components can also emit events, allowing them to communicate with their parent components. Let's create a component that dispatches a custom event when a button is clicked:

```svelte
<script>
  import { createEventDispatcher } from 'svelte';

  const dispatch = createEventDispatcher();

  function handleClick() {
    dispatch('message', {
      text: 'Button clicked!'
    });
  }
</script>

<button on:click={handleClick}>Click Me</button>
```

Here, `createEventDispatcher` creates a function that we can use to dispatch custom events. The `dispatch('message', { text: 'Button clicked!' })` call dispatches an event named 'message' with some data.

In the parent component, you can listen for this event:

```svelte
<script>
  import MyButton from './MyButton.svelte';

  function handleMessage(event) {
    alert(event.detail.text);
  }
</script>

<MyButton on:message={handleMessage} />
```

The `on:message={handleMessage}` syntax binds the `handleMessage` function to the 'message' event dispatched by the `MyButton` component.  When the button is clicked, the alert will display "Button clicked!". The `event.detail` property contains the data passed along with the event.

## Bindings

Svelte offers a powerful feature called "bindings" that allow you to synchronize data between components and DOM elements.  One common use case is two-way binding with input fields.

```svelte
<script>
  let name = '';
</script>

<input type="text" bind:value={name}>

<p>You typed: {name}</p>
```

In this example, the `bind:value={name}` directive creates a two-way binding between the input field's `value` property and the `name` variable.  Whenever the user types something into the input field, the `name` variable is automatically updated, and vice versa.

## Lifecycle Hooks

Svelte provides lifecycle hooks that allow you to execute code at specific points in a component's lifecycle. Some of the most commonly used hooks include:

*   **`onMount`**:  Called after the component is first rendered to the DOM. Useful for performing initialization tasks that require the DOM to be available.
*   **`onDestroy`**: Called when the component is about to be unmounted from the DOM.  Useful for cleaning up resources, such as timers or event listeners.
*   **`beforeUpdate`**: Called immediately before the component's DOM is updated.
*   **`afterUpdate`**: Called immediately after the component's DOM has been updated.

Here's an example of using `onMount` and `onDestroy`:

```svelte
<script>
  import { onMount, onDestroy } from 'svelte';

  let timer;

  onMount(() => {
    timer = setInterval(() => {
      console.log('Timer tick');
    }, 1000);

    return () => {
      clearInterval(timer);
    };
  });

  onDestroy(() => {
    clearInterval(timer);
  });
</script>

<p>This component has a timer running in the background.</p>
```

In this example, `onMount` starts a timer that logs a message to the console every second.  The `onDestroy` hook clears the timer when the component is unmounted, preventing memory leaks.  Note that `onMount` can optionally return a function that will be called during `onDestroy`.

## Common Challenges and Solutions

*   **Prop Drilling:** Passing props through multiple layers of components can become cumbersome. Consider using Svelte's context API or a dedicated state management library (like Svelte Store) for managing shared state.

*   **Reactivity Issues:** Svelte's reactivity system is generally very efficient, but sometimes you might encounter unexpected behavior.  Make sure you are correctly updating variables and that Svelte can track the dependencies.  Using the `$:` reactive declarations can help to explicitly define dependencies.

*   **Component Re-rendering:** Understanding when and why Svelte re-renders components is crucial for performance optimization. Use the Svelte Devtools to inspect component updates and identify potential bottlenecks.  Consider using `{#each}` blocks with `key` attributes for efficient list rendering.

## Engaging with the Material

To solidify your understanding of Svelte components, try the following exercises:

1.  **Create a reusable input component:**  Build a component that encapsulates an input field with a label and error message.  Use props to customize the label, input type, and validation rules.
2.  **Implement a simple counter component:** Create a component with a button that increments a counter.  Display the current count value.  Add a reset button that sets the count back to zero.
3.  **Build a to-do list component:**  Create a component that allows users to add, remove, and mark to-do items as complete.  Use Svelte's `{#each}` block to render the list of to-do items.

## Resources

*   **Svelte Tutorial:** The official Svelte tutorial is an excellent resource for learning the fundamentals of Svelte: [https://svelte.dev/tutorial](https://svelte.dev/tutorial)
*   **Svelte Documentation:** The Svelte documentation provides comprehensive information about all aspects of the framework: [https://svelte.dev/docs](https://svelte.dev/docs)
*   **Svelte REPL:** Experiment with Svelte code online using the Svelte REPL: [https://svelte.dev/repl](https://svelte.dev/repl)

## Summary

Svelte components are the fundamental building blocks of Svelte applications. By mastering the concepts of component creation, props, events, bindings, and lifecycle hooks, you can build complex and maintainable user interfaces with ease. Remember to practice building components and experiment with different techniques to deepen your understanding of the framework. The provided resources and exercises will help you on your journey to becoming a proficient Svelte developer.
