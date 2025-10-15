# Svelte Stores for State Management

Svelte stores provide a powerful and reactive way to manage application state. They allow components to subscribe to changes in the state and automatically update when those changes occur. This simplifies the process of sharing data between components and keeping the UI in sync with the underlying data.

At its core, a Svelte store is just an object with a `subscribe` method. This method allows components to register a callback function that will be executed whenever the store's value changes. Svelte also provides several built-in store types, such as writable, readable, and derived stores, each tailored for specific use cases. These built-in stores provide convenient methods for updating and managing the store's value.

## Understanding Writable Stores

Writable stores are the most common type of store and allow you to both read and update the store's value. To create a writable store, you can use the `writable` function from the `svelte/store` module.

```javascript
import { writable } from 'svelte/store';

const count = writable(0); // Initial value is 0
```

The `writable` function returns an object with three methods:

*   `subscribe`: Used to subscribe to changes in the store's value.
*   `set`: Used to directly set the store's value.
*   `update`: Used to update the store's value based on its previous value.

Here's an example of how to use these methods:

```svelte
<script>
  import { writable } from 'svelte/store';

  const count = writable(0);

  function increment() {
    count.update(n => n + 1);
  }

  function decrement() {
    count.update(n => n - 1);
  }

  function reset() {
    count.set(0);
  }
</script>

<h1>Count: {$count}</h1>
<button on:click={increment}>Increment</button>
<button on:click={decrement}>Decrement</button>
<button on:click={reset}>Reset</button>
```

In this example, the `$count` syntax is used to automatically subscribe to the `count` store. Whenever the store's value changes, the component will automatically re-render, updating the displayed count.  This is called auto-subscription.

## Readable Stores for Read-Only Data

Readable stores are designed for situations where you want to expose a value that can be read but not directly modified from outside the store. The value is set during store creation and only updated by the store itself. This is useful for managing data that is derived from external sources or calculated internally.

```javascript
import { readable } from 'svelte/store';

const startTime = readable(new Date(), function start(set) {
  const interval = setInterval(() => {
    set(new Date());
  }, 1000);

  return function stop() {
    clearInterval(interval);
  };
});
```

In this example, the `readable` store emits the current date and time every second. The `start` function is called when the first subscriber connects to the store, and the `stop` function is called when the last subscriber disconnects. This ensures that the interval is only running when necessary.

```svelte
<script>
  import { startTime } from './stores.js';
</script>

<p>The time is {$startTime}</p>
```

## Derived Stores for Computed Values

Derived stores allow you to create a new store whose value is derived from one or more existing stores. This is useful for creating computed properties that automatically update when their dependencies change.

```javascript
import { writable, derived } from 'svelte/store';

const width = writable(100);
const height = writable(50);

const area = derived(
  [width, height],
  ([$width, $height]) => $width * $height
);
```

In this example, the `area` store is derived from the `width` and `height` stores. Whenever the value of either `width` or `height` changes, the `area` store will automatically update.

```svelte
<script>
  import { width, height, area } from './stores.js';
</script>

<label>Width: <input type="number" bind:value={$width}></label>
<label>Height: <input type="number" bind:value={$height}></label>

<p>Area: {$area}</p>
```

## Custom Stores: Tailoring State Management

While the built-in stores cover many common use cases, you can also create custom stores to manage more complex state. A custom store is simply an object with a `subscribe` method. You can add additional methods to the store to provide custom logic for updating the store's value.

```javascript
import { writable } from 'svelte/store';

function createTodoStore() {
  const { subscribe, set, update } = writable([]);

  return {
    subscribe,
    add: (text) => {
      update(todos => [...todos, { text, completed: false, id: Date.now() }]);
    },
    toggle: (id) => {
      update(todos =>
        todos.map(todo =>
          todo.id === id ? { ...todo, completed: !todo.completed } : todo
        )
      );
    },
    remove: (id) => {
      update(todos => todos.filter(todo => todo.id !== id));
    },
    clearCompleted: () => {
      update(todos => todos.filter(todo => !todo.completed));
    }
  };
}

export const todos = createTodoStore();
```

This example creates a custom store for managing a list of todo items. The store provides methods for adding, toggling, removing, and clearing todo items.

```svelte
<script>
  import { todos } from './stores.js';

  let newTodo = '';

  function addTodo() {
    if (newTodo.trim()) {
      todos.add(newTodo);
      newTodo = '';
    }
  }
</script>

<input type="text" bind:value={newTodo} on:keydown={(e) => e.key === 'Enter' && addTodo()} placeholder="Add todo">
<ul>
  {#each $todos as todo (todo.id)}
    <li>
      <label>
        <input type="checkbox" bind:checked={todo.completed}>
        {todo.text}
      </label>
    </li>
  {/each}
</ul>
```

## Common Challenges and Solutions

*   **Store is not updating:** Ensure that you are using the `set` or `update` methods to modify the store's value.  Directly modifying the store's value will not trigger updates.
*   **Unnecessary re-renders:** Avoid creating derived stores that depend on frequently changing values unless necessary.  Optimize derived store logic to minimize computations.
*   **Memory leaks:** When using readable stores with intervals or other long-running processes, make sure to clean up resources in the `stop` function to prevent memory leaks.
*   **Async operations:**  When updating stores with asynchronous operations, handle potential errors and loading states appropriately.

## External Resources

*   [Svelte Tutorial - Stores](https://svelte.dev/tutorial/writable-stores):  The official Svelte tutorial on stores.
*   [Svelte Docs - Stores](https://svelte.dev/docs/svelte-store): The official Svelte documentation on stores.

## Engagement

Consider these questions as you work with Svelte stores:

*   How can stores help decouple components in my application?
*   What are the trade-offs between using global stores and passing props down through components?
*   How can I use derived stores to create complex computed properties?
*   When should I consider creating a custom store instead of using the built-in store types?

## Summary

Svelte stores are a fundamental part of building reactive and maintainable Svelte applications. By understanding the different types of stores and how to use them effectively, you can simplify state management and create more robust and scalable applications. They provide a centralized and reactive way to manage application state, making it easier to share data between components and keep the UI in sync. Whether you're using writable stores for mutable data, readable stores for read-only data, or derived stores for computed values, Svelte stores provide the tools you need to manage your application's state effectively.