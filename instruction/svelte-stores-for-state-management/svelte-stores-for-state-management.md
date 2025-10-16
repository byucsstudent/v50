# Svelte Stores for State Management

Svelte stores provide a powerful and reactive way to manage application state. They allow different components in your application to share and react to changes in data without the complexity of prop drilling or context providers. This lesson will guide you through the fundamentals of Svelte stores, demonstrating how to create, use, and customize them for effective state management.

Svelte components are naturally reactive. When a component's data changes, Svelte efficiently updates the DOM. However, when data needs to be shared between components that aren't directly related, a more centralized solution is needed. That's where stores come in. Stores are simply objects that hold state and allow components to subscribe to that state. When the state changes, all subscribers are automatically notified and can update themselves accordingly.

## Store Basics

At their core, Svelte stores are objects with a `subscribe` method. This method allows components to register a callback function that will be executed whenever the store's value changes. Svelte provides three built-in store types: `writable`, `readable`, and `derived`.

### Writable Stores

The `writable` store is the most common type. It allows you to both read and update its value. You create a `writable` store using the `writable` function from the `svelte/store` module.

```javascript
// store.js
import { writable } from 'svelte/store';

export const count = writable(0);
```

In this example, we've created a `writable` store named `count` initialized with a value of `0`. To use this store in a component, you import it and subscribe to it using the `$` prefix, which is a Svelte shorthand for subscribing to a store.

```svelte
<!-- MyComponent.svelte -->
<script>
  import { count } from './store.js';

  function increment() {
    count.update(n => n + 1);
  }
</script>

<h1>Count: {$count}</h1>
<button on:click={increment}>Increment</button>
```

The `count.update` method takes a function as an argument. This function receives the current value of the store and returns the new value.  Alternatively, you can use `count.set(newValue)` to directly set a new value.

### Readable Stores

A `readable` store allows you to read its value, but not directly modify it from outside the store's definition.  This is useful for state that is derived from external sources or calculated in a controlled manner.  You create a `readable` store using the `readable` function.

```javascript
// time.js
import { readable } from 'svelte/store';

export const time = readable(new Date(), function start(set) {
  const interval = setInterval(() => {
    set(new Date());
  }, 1000);

  return function stop() {
    clearInterval(interval);
  };
});
```

The `readable` function takes an initial value and a `start` function. The `start` function receives a `set` function, which you use to update the store's value.  The `start` function should return a `stop` function, which is called when the last subscriber unsubscribes from the store. This allows you to clean up resources, such as clearing intervals, when the store is no longer in use.

```svelte
<!-- TimeComponent.svelte -->
<script>
  import { time } from './time.js';
</script>

<h1>Current Time: {$time}</h1>
```

### Derived Stores

`Derived` stores calculate their value based on one or more other stores. They automatically update whenever the dependencies change. This is perfect for creating computed properties in your application. Use the `derived` function to create them.

```javascript
// derivedStore.js
import { derived } from 'svelte/store';
import { count } from './store.js';

export const doubleCount = derived(count, $count => $count * 2);
```

Here, `doubleCount` is a derived store that always contains twice the value of the `count` store.  The first argument to `derived` is the dependency store (or an array of dependency stores), and the second argument is a function that receives the values of the dependency stores and returns the derived value.

```svelte
<!-- DerivedComponent.svelte -->
<script>
  import { doubleCount } from './derivedStore.js';
</script>

<h1>Double Count: {$doubleCount}</h1>
```

You can also derive from multiple stores:

```javascript
// multipleDerivedStore.js
import { derived } from 'svelte/store';
import { count } from './store.js';
import { time } from './time.js';

export const combined = derived([count, time], ([$count, $time]) => {
  return `Count: ${$count}, Time: ${$time.toLocaleTimeString()}`;
});
```

## Custom Stores

While the built-in store types are often sufficient, you can create custom stores to encapsulate more complex logic. A custom store is simply an object with a `subscribe` method. You can add other methods to your store to provide custom functionality.

```javascript
// customStore.js
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
    }
  };
}

export const todos = createTodoStore();
```

In this example, we've created a custom store called `todos` that manages a list of todo items. The store has methods for adding, toggling, and removing todos.  Internally, it uses a `writable` store to hold the todo data, but it exposes a more specific API to components.

```svelte
<!-- TodoComponent.svelte -->
<script>
  import { todos } from './customStore.js';

  let newTodo = '';

  function addTodo() {
    todos.add(newTodo);
    newTodo = '';
  }
</script>

<input type="text" bind:value={newTodo} />
<button on:click={addTodo}>Add Todo</button>

<ul>
  {#each $todos as todo (todo.id)}
    <li>
      <input type="checkbox" bind:checked={todo.completed} on:change={() => todos.toggle(todo.id)} />
      {todo.text}
      <button on:click={() => todos.remove(todo.id)}>Remove</button>
    </li>
  {/each}
</ul>
```

## Common Challenges and Solutions

*   **Incorrect Store Updates:**  A common mistake is to directly modify the store's value without using the `set` or `update` methods. This can lead to reactivity issues. Always use `set` or `update` to ensure that Svelte is aware of the changes.

*   **Unnecessary Store Subscriptions:** Subscribing to a store in a component that doesn't need its value can lead to performance issues. Only subscribe to stores in components that actively use their data.

*   **Forgetting to Unsubscribe:** Although Svelte automatically unsubscribes from stores when a component is destroyed if you use the `$` syntax, if you manually subscribe to a store using `store.subscribe()`, you *must* unsubscribe in the `onDestroy` lifecycle hook to prevent memory leaks.

    ```svelte
    <script>
        import { onMount, onDestroy } from 'svelte';
        import { myStore } from './store';

        let value;
        let unsubscribe;

        onMount(() => {
            unsubscribe = myStore.subscribe(val => {
                value = val;
            });
        });

        onDestroy(() => {
            unsubscribe();
        });
    </script>

    <p>{value}</p>
    ```

*   **Complex Derived Store Logic:** When derived store logic becomes too complex, consider breaking it down into smaller, more manageable derived stores.  This improves readability and maintainability.

## Tips for Effective State Management

*   **Centralize Your Stores:**  Create a dedicated `store` directory in your project to house all your stores. This makes it easier to locate and manage your application's state.

*   **Use Descriptive Store Names:** Choose store names that clearly indicate the data they hold.  For example, `user` instead of `data`.

*   **Encapsulate Logic within Stores:** Keep your components focused on presentation and interaction.  Move complex data manipulation logic into your stores.

*   **Consider Third-Party Store Libraries:**  For larger applications, explore libraries like `svelte-store-inject` or `nanostores` which provide advanced features such as persistence, undo/redo functionality, and more.

## External Resources

*   **Svelte Tutorial - Stores:** [https://svelte.dev/tutorial/writable-stores](https://svelte.dev/tutorial/writable-stores)
*   **Svelte Documentation - Stores:** [https://svelte.dev/docs/svelte-store](https://svelte.dev/docs/svelte-store)

## Summary

Svelte stores offer a robust and reactive solution for managing application state. By understanding the different store types (`writable`, `readable`, and `derived`) and how to create custom stores, you can effectively share data between components and build complex applications with ease. Remember to use `set` and `update` for modifying store values, unsubscribe from stores manually when necessary, and keep your store logic organized and maintainable.  Experiment with these concepts to discover how stores can streamline your Svelte development workflow.