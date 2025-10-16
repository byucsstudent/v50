# Actions

Svelte actions are a powerful tool that allows you to manipulate a DOM node directly when it's created and automatically clean up when the node is destroyed. They provide a way to encapsulate complex DOM interactions and reuse them across your components. Think of them as a way to extend the functionality of HTML elements in a declarative and maintainable way.

Actions are functions that are called when an element is added to the DOM. These functions can modify the element, add event listeners, or perform any other DOM manipulation. Crucially, they can also return an object with a `destroy` method, which Svelte will call when the element is removed from the DOM. This allows you to clean up any resources you allocated, such as event listeners or timers, preventing memory leaks.

## Defining and Using Actions

An action is simply a function that takes two arguments:

*   `node`: The DOM node that the action is applied to.
*   `parameters` (optional): Any parameters passed to the action.

The action function returns an object with an optional `update` and `destroy` method.

Here's a basic example of an action that sets the focus on an input element when it's mounted:

```svelte
<script>
  function focus(node) {
    node.focus();
    return {
      destroy() {
        // Any cleanup logic here
      }
    };
  }
</script>

<input type="text" use:focus />
```

In this example, `use:focus` tells Svelte to call the `focus` function with the input element as its argument.  The `focus` function then calls `node.focus()` to set the focus. The `destroy` function, although empty here, is important for cleanup if needed.

## Actions with Parameters

Actions can also accept parameters. This allows you to configure the action's behavior based on the context in which it's used.

```svelte
<script>
  function tooltip(node, text) {
    let tooltipText = text;
    let tooltipElement;

    function updateTooltip(newText) {
        tooltipText = newText;
        if (tooltipElement) {
            tooltipElement.textContent = tooltipText;
        }
    }

    function showTooltip(event) {
      tooltipElement = document.createElement('div');
      tooltipElement.textContent = tooltipText;
      tooltipElement.style.position = 'absolute';
      tooltipElement.style.top = `${event.pageY + 10}px`;
      tooltipElement.style.left = `${event.pageX + 10}px`;
      tooltipElement.style.background = 'black';
      tooltipElement.style.color = 'white';
      tooltipElement.style.padding = '5px';
      tooltipElement.style.borderRadius = '5px';
      document.body.appendChild(tooltipElement);
    }

    function hideTooltip() {
      if (tooltipElement) {
        tooltipElement.remove();
        tooltipElement = null;
      }
    }

    node.addEventListener('mouseover', showTooltip);
    node.addEventListener('mouseout', hideTooltip);

    return {
      update(newText) {
        updateTooltip(newText);
      },
      destroy() {
        node.removeEventListener('mouseover', showTooltip);
        node.removeEventListener('mouseout', hideTooltip);
        hideTooltip(); // Ensure tooltip is removed on destroy
      }
    };
  }
</script>

<p use:tooltip="'This is a tooltip!'">Hover over me</p>
<p use:tooltip="'Another tooltip here.'">Hover over this one</p>

<script>
    let dynamicText = "Dynamic Tooltip Text";
</script>

<input use:tooltip={dynamicText} bind:value={dynamicText}>
```

In this example, the `tooltip` action takes the DOM node and tooltip text as parameters. It creates a tooltip element and adds it to the body when the user hovers over the element. The `destroy` function removes the event listeners and the tooltip element when the element is removed from the DOM. The `update` function allows the tooltip text to be dynamically updated.

## The `update` Method

As shown in the `tooltip` example, the returned object can contain an `update` method. This method is called whenever the parameters passed to the action change. This allows you to update the action's behavior based on new parameter values.  It is important to note that the `update` method is only available if the action is called with parameters.

## Common Use Cases

Actions are useful for a variety of tasks, including:

*   **Focus management:** Setting focus on an element when it's mounted.
*   **Third-party library integration:**  Wrapping interactions with libraries like Leaflet or Chart.js.
*   **Custom event handling:**  Creating custom events that are triggered by user interactions.
*   **Drag and drop:** Implementing drag-and-drop functionality.
*   **Animations:** Triggering animations when an element is mounted or unmounted.

## Common Challenges and Solutions

*   **Memory leaks:** Forgetting to clean up event listeners or timers in the `destroy` function can lead to memory leaks. Always ensure that you clean up any resources you allocate in the action.
*   **Unexpected behavior:** Actions can sometimes interact in unexpected ways with other parts of your application. Be careful when modifying the DOM directly and test your actions thoroughly.
*   **Performance:**  Complex actions can impact performance, especially if they are applied to many elements. Optimize your actions to minimize the amount of DOM manipulation they perform.
*   **Passing complex data:** When passing complex data as parameters to the action, Svelte will perform a deep equality check to determine if the parameters have changed. For large objects, this can impact performance. Consider using a store to manage the data and pass the store as a parameter to the action.

## Alternative Approaches

While actions are useful, consider these alternative approaches:

*   **Components:** For complex logic or reusable UI elements, consider creating a Svelte component instead of an action.
*   **Event handlers:** For simple event handling, you can use Svelte's built-in event handlers.
*   **Stores:**  For managing application state, use Svelte stores.

Actions are best suited for encapsulating DOM manipulation logic that is specific to a particular element.

## External Resources

*   [Svelte Tutorial - Actions](https://svelte.dev/tutorial/actions)
*   [Svelte Documentation - Actions](https://svelte.dev/docs/svelte-action)

## Thoughtful Engagement

Consider the following questions as you continue learning about actions:

*   When is it appropriate to use an action instead of a component?
*   How can you use actions to improve the reusability and maintainability of your code?
*   What are some potential performance implications of using actions, and how can you mitigate them?
*   Can you think of other use cases for actions beyond the examples provided?

## Summary

Svelte actions offer a powerful mechanism for directly manipulating DOM nodes and encapsulating complex DOM interactions. They provide a way to extend the functionality of HTML elements in a declarative and reusable manner. By understanding how to define, use, and clean up actions, you can create more maintainable and efficient Svelte applications. Remember to carefully consider the potential performance implications of using actions and to choose the right tool for the job, considering components, event handlers, and stores as alternative approaches.