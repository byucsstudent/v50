# React Hooks: useState, useEffect, useContext

React Hooks are a revolutionary addition to React, introduced in version 16.8. They allow you to use state and other React features in functional components, which were previously only available in class components. This makes your code more concise, readable, and testable. This guide will cover three of the most fundamental hooks: `useState`, `useEffect`, and `useContext`.

## Understanding Hooks

Before Hooks, managing state and side effects in functional components was challenging. Class components, while powerful, often led to verbose and complex code. Hooks provide a more elegant and functional approach, enabling you to write React components with less boilerplate. They promote code reuse and simplify component logic.

## The `useState` Hook

The `useState` hook is the cornerstone of state management in functional components. It allows you to declare and update state variables directly within your functional component.

### Basic Usage

To use `useState`, you first need to import it from the `react` library:

```javascript
import React, { useState } from 'react';
```

The `useState` hook takes an initial value as an argument and returns an array containing two elements:

1.  The current state value.
2.  A function to update that state value.

Here's a simple example:

```javascript
import React, { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}

export default Counter;
```

In this example:

*   `useState(0)` initializes the state variable `count` with a value of `0`.
*   `count` holds the current value of the counter.
*   `setCount` is a function that allows you to update the `count` state.
*   When the "Increment" button is clicked, `setCount(count + 1)` is called, which updates the `count` state, causing the component to re-render and display the new value.

### Updating State

The `setCount` function (or its equivalent in your component) is crucial for updating the state. When you call `setCount`, React schedules a re-render of the component.

**Important Note:** When updating state based on the previous state, it's best practice to use a function as the argument to the setter function. This ensures that you're working with the most up-to-date value, especially in asynchronous scenarios.

```javascript
<button onClick={() => setCount(prevCount => prevCount + 1)}>Increment</button>
```

Using `prevCount => prevCount + 1` guarantees that you're incrementing the correct previous value, even if multiple updates are batched together.

### Multiple State Variables

You can use `useState` multiple times within a single component to manage different pieces of state:

```javascript
import React, { useState } from 'react';

function Profile() {
  const [name, setName] = useState('John Doe');
  const [age, setAge] = useState(30);

  return (
    <div>
      <p>Name: {name}</p>
      <p>Age: {age}</p>
      <button onClick={() => setName('Jane Doe')}>Update Name</button>
      <button onClick={() => setAge(35)}>Update Age</button>
    </div>
  );
}

export default Profile;
```

In this example, we're managing both `name` and `age` as separate state variables.

### Common Challenges with `useState`

*   **Incorrectly updating state based on the previous state:** As mentioned earlier, always use a function when updating state that depends on the previous value.

*   **Forgetting to import `useState`:** This will result in a runtime error.  Always double-check your imports.

*   **Not triggering re-renders:** React only re-renders a component when the state value changes. If you're updating an object or array, make sure to create a new object or array instead of modifying the existing one directly.  Use the spread operator or other immutable update techniques.

## The `useEffect` Hook

The `useEffect` hook allows you to perform side effects in functional components. Side effects are operations that interact with the outside world, such as fetching data from an API, setting up subscriptions, or directly manipulating the DOM.

### Basic Usage

Like `useState`, you need to import `useEffect` from the `react` library:

```javascript
import React, { useState, useEffect } from 'react';
```

The `useEffect` hook takes two arguments:

1.  A function containing the side effect logic.
2.  An optional array of dependencies.

```javascript
import React, { useState, useEffect } from 'react';

function DataFetcher() {
  const [data, setData] = useState(null);

  useEffect(() => {
    // Simulate fetching data from an API
    setTimeout(() => {
      setData({ message: 'Data fetched successfully!' });
    }, 2000);
  }, []); // Empty dependency array means this effect runs only once on mount

  return (
    <div>
      {data ? <p>{data.message}</p> : <p>Loading...</p>}
    </div>
  );
}

export default DataFetcher;
```

In this example:

*   `useEffect(() => { ... }, [])` defines a side effect that runs after the component mounts.
*   The empty dependency array `[]` tells React to run this effect only once, similar to `componentDidMount` in class components.
*   Inside the effect, we're simulating fetching data from an API using `setTimeout`.
*   When the data is fetched, we update the `data` state, causing the component to re-render and display the message.

### Dependencies

The dependency array is crucial for controlling when the effect runs. If you provide dependencies, the effect will only run when those dependencies change.

```javascript
import React, { useState, useEffect } from 'react';

function CounterWithEffect() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    document.title = `Count: ${count}`;
  }, [count]); // Effect runs whenever 'count' changes

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}

export default CounterWithEffect;
```

In this example:

*   `useEffect(() => { ... }, [count])` defines a side effect that updates the document title whenever the `count` state changes.
*   The `[count]` dependency array tells React to run this effect only when the `count` value changes.

### Cleanup

Many side effects require cleanup, such as removing event listeners or canceling subscriptions. You can return a cleanup function from the `useEffect` hook. This function will be called when the component unmounts or before the effect runs again with new dependencies.

```javascript
import React, { useState, useEffect } from 'react';

function Subscription() {
  const [message, setMessage] = useState('');

  useEffect(() => {
    const subscription = {
      //Simulate Subscription
      subscribe: (callback) => {
        console.log("Subscribed");
        callback("New Message!");
        return {
          unsubscribe: () => {
            console.log("Unsubscribed");
          }
        }
      }
    };

    const sub = subscription.subscribe(msg => {
      setMessage(msg);
    });

    return () => {
      sub.unsubscribe(); // Cleanup function
    };
  }, []);

  return (
    <div>
      <p>Message: {message}</p>
    </div>
  );
}

export default Subscription;
```

In this example:

*   The `useEffect` hook sets up a subscription.
*   The cleanup function `return () => { sub.unsubscribe(); }` is called when the component unmounts, ensuring that the subscription is properly cleaned up.

### Common Challenges with `useEffect`

*   **Infinite loops:**  If your effect updates a state variable that is also a dependency, it can trigger an infinite loop of re-renders.  Carefully consider your dependencies.

*   **Forgetting dependencies:**  If you omit dependencies, your effect might not run when it should, or it might run with stale values.

*   **Not cleaning up:**  Failing to clean up side effects can lead to memory leaks or unexpected behavior. Always provide a cleanup function when necessary.

## The `useContext` Hook

The `useContext` hook allows you to access values from a React context without needing to use a `Context.Consumer`. This simplifies accessing shared data throughout your component tree.

### Basic Usage

First, you need to create a context using `React.createContext`:

```javascript
import React, { createContext } from 'react';

const ThemeContext = createContext('light'); // Default value
```

Then, you can provide values to the context using a `Context.Provider`:

```javascript
import React, { useState } from 'react';
import ThemeContext from './ThemeContext'; // Assuming ThemeContext is in a separate file

function App() {
  const [theme, setTheme] = useState('light');

  return (
    <ThemeContext.Provider value={{ theme, setTheme }}>
      {/* Your components here */}
    </ThemeContext.Provider>
  );
}

export default App;
```

Finally, you can access the context value using the `useContext` hook:

```javascript
import React, { useContext } from 'react';
import ThemeContext from './ThemeContext';

function ThemedComponent() {
  const { theme, setTheme } = useContext(ThemeContext);

  return (
    <div style={{ backgroundColor: theme === 'light' ? '#fff' : '#000', color: theme === 'light' ? '#000' : '#fff' }}>
      <p>Current Theme: {theme}</p>
      <button onClick={() => setTheme(theme === 'light' ? 'dark' : 'light')}>Toggle Theme</button>
    </div>
  );
}

export default ThemedComponent;
```

In this example:

*   `const { theme, setTheme } = useContext(ThemeContext)` accesses the `theme` and `setTheme` values from the `ThemeContext`.
*   The `ThemedComponent` can then use these values to style itself and toggle the theme.

### When to Use `useContext`

`useContext` is ideal for sharing data that is considered "global" for a tree of React components, such as:

*   Theme settings
*   User authentication status
*   Language preferences

### Common Challenges with `useContext`

*   **Overusing Context:** While `useContext` is convenient, avoid using it for every piece of data.  Overuse can lead to tight coupling and make your components harder to test and reuse.

*   **Context Value Not Updating:** Ensure that the context provider is re-rendering when the value changes.  If the provider doesn't re-render, the components consuming the context will not update.

*   **Forgetting to Provide a Default Value:**  Providing a default value to `createContext` is good practice as it acts as a fallback if a component tries to access the context outside of a Provider.

## Summary

React Hooks provide a powerful and elegant way to manage state and side effects in functional components. `useState` allows you to declare and update state variables, `useEffect` enables you to perform side effects, and `useContext` simplifies accessing shared data throughout your component tree. By understanding and effectively using these hooks, you can write more concise, readable, and maintainable React code. Keep practicing with different scenarios and examples to solidify your understanding of these essential tools.

## Further Exploration

*   **React Documentation on Hooks:** [https://react.dev/reference/react](https://react.dev/reference/react)
*   **Advanced Hook Patterns:** Explore custom hooks and more complex use cases to further enhance your skills.
*   **Performance Considerations:**  Learn how to optimize your hook usage for better performance.