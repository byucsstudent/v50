# Computed Properties

Computed properties are a powerful feature in Svelte that allow you to derive new values from existing reactive state. They are essentially functions that automatically update whenever their dependencies change. This makes them incredibly useful for creating dynamic and responsive user interfaces without manually managing complex state updates.  Think of them as reactive formulas that automatically recalculate when their inputs change.

Computed properties promote clean, maintainable, and efficient code.  They help avoid redundant calculations and improve performance by only updating when necessary. They are declared using the `$:` syntax, which Svelte uses to mark reactive statements.

## Basic Usage

Let's start with a simple example. Suppose you have a `firstName` and `lastName` variable, and you want to create a `fullName` variable that automatically updates whenever either the first or last name changes. Here's how you would do it:

```svelte
<script>
  let firstName = 'John';
  let lastName = 'Doe';

  $: fullName = `${firstName} ${lastName}`;
</script>

<h1>Hello, {fullName}!</h1>

<input bind:value={firstName} placeholder="First Name">
<input bind:value={lastName} placeholder="Last Name">
```

In this example, `$: fullName = `${firstName} ${lastName}`;` is a reactive statement.  Whenever `firstName` or `lastName` changes (due to the input fields), `fullName` is automatically recalculated.  Svelte efficiently manages the dependency tracking, ensuring that `fullName` is only updated when its dependencies actually change.

## Dependencies

Computed properties automatically track their dependencies. Svelte analyzes the code within the reactive statement to determine which variables are being used.  Any change to these variables will trigger a re-evaluation of the computed property.

Consider this example:

```svelte
<script>
  let price = 10;
  let quantity = 2;

  $: total = price * quantity;
</script>

<p>Price: {price}</p>
<p>Quantity: {quantity}</p>
<p>Total: {total}</p>

<input type="number" bind:value={price}>
<input type="number" bind:value={quantity}>
```

Here, `total` depends on both `price` and `quantity`. Modifying either input field will cause `total` to update. Svelte handles the dependency tracking automatically, so you don't have to manually specify which variables `total` depends on.

## Complex Computations

Computed properties can handle more complex computations, including calling functions and using conditional logic.

```svelte
<script>
  let items = [
    { id: 1, name: 'Apple', price: 1 },
    { id: 2, name: 'Banana', price: 0.5 },
    { id: 3, name: 'Orange', price: 0.75 }
  ];

  let discount = 0.1; // 10% discount

  $: discountedTotal = items.reduce((sum, item) => {
    return sum + item.price * (1 - discount);
  }, 0);

  $: formattedTotal = discountedTotal.toFixed(2);
</script>

<ul>
  {#each items as item (item.id)}
    <li>{item.name}: ${item.price}</li>
  {/each}
</ul>

<p>Discounted Total: ${formattedTotal}</p>

<input type="number" bind:value={discount}>
```

In this example, `discountedTotal` calculates the total price of all items after applying a discount. `formattedTotal` then formats the `discountedTotal` to two decimal places. Both are reactive and will update whenever `items` or `discount` changes. This showcases how computed properties can be chained together to perform more elaborate calculations.

## Common Challenges and Solutions

*   **Circular Dependencies:** One common pitfall is creating circular dependencies, where a computed property depends on itself, directly or indirectly. This can lead to infinite loops and performance issues. Svelte will usually detect and warn you about these situations.  To avoid them, carefully analyze the dependencies of your computed properties and ensure there are no circular paths.  Restructure your code or introduce intermediate variables to break the loop.

    ```svelte
    <script>
      let a = 10;
      $: b = a + c; // Potential circular dependency if c depends on b
      let c = 5;
    </script>
    ```

    The fix would involve ensuring `c` does not depend on `b` or introducing a new variable.

*   **Performance Considerations:** While computed properties are generally efficient, complex computations performed within them can impact performance, especially if they are triggered frequently.  Optimize your computations to avoid unnecessary work. Consider using memoization techniques if a computation is particularly expensive and its inputs don't change frequently.

*   **Unexpected Updates:** Sometimes, a computed property might update when you don't expect it to. This usually happens when the dependency tracking isn't working as you anticipate. Double-check the variables used within the computed property to ensure they are truly the ones you intend to depend on. Using the Svelte Devtools can help visualize the reactive graph and identify unexpected dependencies.

## Alternatives to Computed Properties

While computed properties are frequently the best choice, there are situations where other approaches might be more suitable:

*   **Functions:** For computations that don't need to be reactive, regular functions are a better fit. For example, a function that formats a date string only when explicitly called.

*   **Stores:**  For managing complex state and sharing it across multiple components, Svelte stores are a powerful tool.  Computed properties can be derived from stores using the `derived` function.

*   **Event Handlers:** For computations triggered by specific user interactions (like a button click), event handlers are more appropriate.

## Computed Properties and Stores

Svelte stores are a powerful way to manage application state. You can combine the power of stores and computed properties to create derived stores.  The `derived` function from `svelte/store` creates a store whose value is computed from one or more other stores.

```svelte
<script>
  import { writable, derived } from 'svelte/store';

  const count = writable(0);

  const doubled = derived(count, ($count) => $count * 2);

  function increment() {
    count.update(n => n + 1);
  }
</script>

<button on:click={increment}>Increment</button>

<p>Count: {$count}</p>
<p>Doubled: {$doubled}</p>
```

In this example, `doubled` is a derived store that automatically updates whenever the `count` store changes. The `derived` function takes the `count` store as input and a function that calculates the new value based on the current value of `count` (accessed using `$count`). This is a powerful way to create reactive state that is derived from other state.

## Summary

Computed properties in Svelte are a powerful and efficient way to derive new values from existing reactive state. They automatically track dependencies, update only when necessary, and promote clean, maintainable code. By understanding their basic usage, dependencies, and potential challenges, you can leverage computed properties to build dynamic and responsive user interfaces. Remember to consider alternatives like regular functions or stores when appropriate, and be mindful of circular dependencies and performance considerations. Experiment with different scenarios and explore the Svelte documentation to further enhance your understanding of computed properties.