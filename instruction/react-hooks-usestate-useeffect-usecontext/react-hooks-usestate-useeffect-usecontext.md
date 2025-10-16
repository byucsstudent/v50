# React Hooks: useState, useEffect, useContext

React Hooks are a powerful addition to React that allow you to use state and other React features in functional components. Before Hooks, these features were primarily available in class components. Hooks provide a more straightforward and often more readable way to manage component logic, leading to cleaner and more maintainable code. This module will cover three fundamental hooks: `useState`, `useEffect`, and `useContext`.

## useState: Managing State in Functional Components

The `useState` hook is your go-to for adding state to functional components. It replaces the need for `this.state` and `this.setState` found in class components.

**How it Works:**

`useState` accepts an initial value as an argument and returns an array containing two elements:

1.  The current state value.
2.  A function to update that state value.

**Example:**

```jsx
import React, { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
      <button onClick={() => setCount(count - 1)}>Decrement</button>
    </div>
  );
}

export default Counter;
```

In this example:

*   `useState(0)` initializes the state variable `count` to 0.
*   `count` holds the current value of the state.
*   `setCount` is a function used to update the `count` state.  Calling `setCount(count + 1)` will re-render the component with the new `count` value.

**Updating State:**

It's important to understand how `setCount` updates the state.  When updating state based on the *previous* state, it's best practice to use a function as the argument to `setCount`. This ensures you're working with the most up-to-date value of the state.

**Example (using a function to update state):**

```jsx
import React, { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(prevCount => prevCount + 1)}>Increment</button>
      <button onClick={() => setCount(prevCount => prevCount - 1)}>Decrement</button>
    </div>
  );
}

export default Counter;
```

Using `setCount(prevCount => prevCount + 1)` guarantees that `prevCount` will always be the latest value of `count`, even if multiple updates are queued up. This is especially crucial when dealing with asynchronous operations.

**Common Challenges and Solutions:**

*   **Not updating state correctly:**  Forgetting to use the functional form of `setCount` when updating based on the previous state can lead to unexpected behavior.  *Solution: Always use the functional form (e.g., `setCount(prevCount => prevCount + 1)`) when the new state depends on the old state.*
*   **Incorrect initial state:**  Providing the wrong initial state type (e.g., a string instead of a number) can cause type errors or unexpected behavior. *Solution: Ensure the initial state type matches the expected type of the state variable.*
*   **Performance issues with frequent updates:**  Excessive state updates can lead to performance problems.  *Solution: Optimize your component to minimize unnecessary re-renders. Consider using `useMemo` or `useCallback` (covered in more advanced topics) to memoize expensive computations or prevent unnecessary re-renders of child components.*

## useEffect: Managing Side Effects in Functional Components

The `useEffect` hook allows you to perform side effects in your functional components. Side effects are actions that interact with the outside world, such as fetching data, setting up subscriptions, or directly manipulating the DOM.

**How it Works:**

`useEffect` accepts two arguments:

1.  A function containing the side effect.
2.  An optional array of dependencies.

**Example:**

```jsx
import React, { useState, useEffect } from 'react';

function DataFetcher() {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    async function fetchData() {
      try {
        const response = await fetch('https://jsonplaceholder.typicode.com/todos/1');
        if (!response.ok) {
          throw new Error(`HTTP error! status: ${response.status}`);
        }
        const json = await response.json();
        setData(json);
      } catch (error) {
        setError(error);
      } finally {
        setLoading(false);
      }
    }

    fetchData();
  }, []); // Empty dependency array means this effect runs only once after the initial render

  if (loading) {
    return <p>Loading...</p>;
  }

  if (error) {
    return <p>Error: {error.message}</p>;
  }

  return (
    <div>
      <h1>Data from API:</h1>
      <pre>{JSON.stringify(data, null, 2)}</pre>
    </div>
  );
}

export default DataFetcher;
```

In this example:

*   `useEffect` is used to fetch data from an API.
*   The first argument is a function that performs the data fetching.
*   The second argument is an empty array `[]`. This means the effect will only run once after the component's initial render, similar to `componentDidMount` in class components.

**Dependency Array:**

The dependency array is crucial for controlling when the effect runs.

*   **Empty array `[]`:** The effect runs only once after the initial render.
*   **Array with dependencies `[dependency1, dependency2, ...]`:** The effect runs after the initial render and whenever any of the dependencies change.
*   **No array (omitted):** The effect runs after *every* render.  This can lead to performance issues and infinite loops if not handled carefully.

**Cleaning Up Effects:**

Some side effects require cleanup, such as unsubscribing from subscriptions or clearing timers.  You can return a cleanup function from the effect function. This cleanup function will be executed when the component unmounts or before the effect runs again (if the dependencies have changed).

**Example (with cleanup):**

```jsx
import React, { useState, useEffect } from 'react';

function WindowWidth() {
  const [width, setWidth] = useState(window.innerWidth);

  useEffect(() => {
    const handleResize = () => setWidth(window.innerWidth);
    window.addEventListener('resize', handleResize);

    return () => {
      window.removeEventListener('resize', handleResize);
    };
  }, []);

  return (
    <div>
      <p>Window Width: {width}px</p>
    </div>
  );
}

export default WindowWidth;
```

In this example:

*   The `useEffect` hook adds an event listener to the `resize` event.
*   The cleanup function removes the event listener when the component unmounts, preventing memory leaks.

**Common Challenges and Solutions:**

*   **Infinite loops:**  Omitting the dependency array or including dependencies that are always changing can cause infinite loops. *Solution: Carefully consider the dependencies and ensure they only change when necessary.*
*   **Missing dependencies:**  Forgetting to include a dependency that the effect relies on can lead to stale data or unexpected behavior. *Solution: React will often warn you about missing dependencies. Pay attention to these warnings and add the necessary dependencies.*
*   **Memory leaks:**  Failing to clean up side effects can lead to memory leaks. *Solution: Always return a cleanup function when the effect creates subscriptions, timers, or other resources that need to be cleaned up.*
*   **Fetching data without handling errors:** Not handling potential errors during data fetching can lead to broken components. *Solution: Always include error handling when fetching data.*

## useContext: Accessing Context Values

The `useContext` hook provides a way to access context values within a functional component. Context allows you to share data between components without explicitly passing props through every level of the component tree.

**How it Works:**

`useContext` accepts a context object (created with `React.createContext`) as an argument and returns the current context value for that context.

**Example:**

```jsx
import React, { createContext, useContext, useState } from 'react';

// Create a context
const ThemeContext = createContext();

// Provider component
function ThemeProvider({ children }) {
  const [theme, setTheme] = useState('light');

  const toggleTheme = () => {
    setTheme(prevTheme => (prevTheme === 'light' ? 'dark' : 'light'));
  };

  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  );
}

// Consumer component
function ThemedButton() {
  const { theme, toggleTheme } = useContext(ThemeContext);

  return (
    <button style={{ backgroundColor: theme === 'light' ? '#eee' : '#333', color: theme === 'light' ? '#333' : '#eee' }} onClick={toggleTheme}>
      Toggle Theme (Current: {theme})
    </button>
  );
}

function App() {
  return (
    <ThemeProvider>
      <div>
        <ThemedButton />
      </div>
    </ThemeProvider>
  );
}

export default App;
```

In this example:

*   `ThemeContext` is created using `React.createContext()`.
*   `ThemeProvider` provides the context value (an object containing the `theme` and `toggleTheme` function) to its children.
*   `ThemedButton` uses `useContext(ThemeContext)` to access the context value and use the `theme` and `toggleTheme` function.

**Key Concepts:**

*   **Context Provider:**  The component that provides the context value using `<MyContext.Provider value={/* value */}>`.
*   **Context Consumer:**  The component that consumes the context value using `useContext(MyContext)`.
*   **Context Value:**  The data that is shared through the context.

**Common Challenges and Solutions:**

*   **Forgetting to wrap components in a Provider:** If a component tries to use `useContext` without being wrapped in a corresponding Provider, it will receive the default context value (which is `undefined` if not specified when creating the context). *Solution: Ensure all components that need to access the context are descendants of a Provider for that context.*
*   **Performance issues with frequent context updates:** Frequent updates to the context value can trigger re-renders of all components that consume the context. *Solution: Optimize your context value to minimize unnecessary updates. Consider using techniques like memoization or splitting the context into smaller, more specific contexts.*
*   **Overusing Context:** While context is powerful, it can lead to tightly coupled components if overused.  *Solution: Use context judiciously, only when data truly needs to be shared across multiple levels of the component tree.*

## Summary

`useState`, `useEffect`, and `useContext` are essential React Hooks that empower you to manage state, handle side effects, and share data in functional components. Mastering these hooks will significantly improve your ability to write clean, maintainable, and efficient React code. Remember to pay close attention to dependencies, cleanup functions, and potential performance implications when using these hooks. By applying the concepts and examples discussed in this module, you'll be well-equipped to leverage the power of React Hooks in your projects.

**Further Exploration:**

*   [React Hooks Documentation](https://react.dev/reference/react)
*   [Using the Effect Hook](https://react.dev/reference/react/useEffect)
*   [Using the State Hook](https://react.dev/reference/react/useState)
*   [Using the Context Hook](https://react.dev/reference/react/useContext)

Now, try building a small application that uses all three hooks. For example, a simple to-do list application or a theme switcher. This hands-on experience will solidify your understanding of these fundamental React Hooks. Consider what challenges you encounter and how you address them. This active engagement is key to mastering these powerful tools.