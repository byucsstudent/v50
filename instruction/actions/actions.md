# Actions

Actions in Svelte are a powerful mechanism for manipulating DOM elements directly. They provide a way to execute custom code when an element is created and when it is destroyed, essentially giving you fine-grained control over the lifecycle of a DOM node. They are particularly useful for integrating with third-party libraries, managing focus, or implementing custom animations.

At their core, actions are functions that are invoked when an element is mounted into the DOM. They receive the DOM node as an argument and can optionally return an object with `update` and `destroy` methods. This allows you to perform setup when the element is created, modify its behavior over time, and clean up when it is removed.

### Defining an Action

An action is simply a JavaScript function. It takes the DOM element as its first argument, and optionally, a parameter as its second argument. This parameter can be anything: a string, number, object, or even another function.

```javascript
function myAction(node, parameters) {
  // This code runs when the element is mounted

  return {
    update(newParameters) {
      // This code runs when the parameters change
    },
    destroy() {
      // This code runs when the element is unmounted
    }
  };
}
```

### Using an Action

To use an action, you apply it to an element in your Svelte component using the `use:` directive.

```svelte
<script>
  import { onMount } from 'svelte';

  function myAction(node, initialValue) {
    let currentValue = initialValue;

    function updateValue(newValue) {
      currentValue = newValue;
      node.textContent = newValue;
    }

    onMount(() => {
      node.textContent = currentValue;
    });

    return {
      update(newValue) {
        updateValue(newValue);
      },
      destroy() {
        // Clean up any event listeners or resources
        console.log('Action destroyed');
      }
    };
  }

  let myValue = "Hello, World!";
</script>

<div use:myAction={myValue}></div>

<button on:click={() => myValue = "Goodbye, World!"}>Change Text</button>
```

In this example, `myAction` is applied to a `div` element. When the component mounts, the action is executed.  The `update` method is called when the `myValue` variable changes, updating the text content of the div. When the component is destroyed, the `destroy` method is called.

### Action Parameters

Actions can accept parameters, allowing you to configure their behavior.  Consider an action that sets focus to an element when it's mounted.

```svelte
<script>
  function focus(node) {
    node.focus();

    return {
      destroy() {
        node.blur(); // Remove focus on destroy
      }
    };
  }
</script>

<input use:focus />
```

You can also pass more complex parameters:

```svelte
<script>
  function tooltip(node, text) {
    let tooltipText = text;
    let div;

    function updateTooltip(newText) {
      tooltipText = newText;
      if (div) {
        div.textContent = tooltipText;
      }
    }

    function createTooltip() {
      div = document.createElement('div');
      div.textContent = tooltipText;
      div.style.position = 'absolute';
      div.style.background = 'black';
      div.style.color = 'white';
      div.style.padding = '5px';
      div.style.borderRadius = '5px';
      div.style.zIndex = '1000'; // Ensure it's on top
      document.body.appendChild(div);

      function updatePosition(event) {
        div.style.left = (event.pageX + 10) + 'px';
        div.style.top = (event.pageY + 10) + 'px';
      }

      node.addEventListener('mousemove', updatePosition);
      node.addEventListener('mouseleave', destroyTooltip);

      return { div, updatePosition };
    }

    function destroyTooltip() {
      if (div) {
        document.body.removeChild(div);
        div = null;
      }
    }

    node.addEventListener('mouseenter', createTooltip);

    function cleanup() {
      node.removeEventListener('mouseenter', createTooltip);
      destroyTooltip();
    }

    return {
      update(newText) {
        updateTooltip(newText);
      },
      destroy() {
        cleanup();
      }
    };
  }

  let message = "This is a tooltip!";
</script>

<p use:tooltip={message}>Hover over me</p>

<input bind:value={message} />
```

In this tooltip example, the action takes the tooltip text as a parameter.  The action creates a `div` element, positions it, and displays the text.  The `update` function ensures the tooltip text can be dynamically changed. The `destroy` function removes the tooltip from the DOM.

### Common Challenges and Solutions

*   **Memory Leaks:** If you're adding event listeners or creating resources in your action, make sure to clean them up in the `destroy` method to prevent memory leaks.  Always remove event listeners and release any resources.

*   **Incorrect `this` Context:** Be mindful of the `this` context inside your action. If you're using methods that rely on a specific `this` value, use `.bind(this)` or arrow functions to maintain the correct context.

*   **Action Not Firing:** Ensure that the element you are applying the action to is actually being rendered.  Conditional rendering or asynchronous data loading can sometimes prevent the action from being executed.

*   **Parameter Updates Not Triggering:**  If your `update` method isn't being called when the parameters change, double-check that the parameters are actually changing and that Svelte is correctly detecting the change. Using immutable data structures can help Svelte track changes more efficiently.

### Integrating with Third-Party Libraries

Actions are excellent for integrating with JavaScript libraries that directly manipulate the DOM. For instance, you can use them to initialize a charting library on a specific element.

```svelte
<script>
  import { onMount } from 'svelte';
  import Chart from 'chart.js/auto'; // Or your preferred charting library

  function chart(node, data) {
    let chartInstance;

    onMount(() => {
      chartInstance = new Chart(node, data);
    });

    return {
      update(newData) {
        chartInstance.data = newData;
        chartInstance.update();
      },
      destroy() {
        chartInstance.destroy();
      }
    };
  }

  let chartData = {
    type: 'bar',
    data: {
      labels: ['Red', 'Blue', 'Yellow', 'Green', 'Purple', 'Orange'],
      datasets: [{
        label: '# of Votes',
        data: [12, 19, 3, 5, 2, 3],
        borderWidth: 1
      }]
    },
    options: {
      scales: {
        y: {
          beginAtZero: true
        }
      }
    }
  };
</script>

<canvas use:chart={chartData}></canvas>

<button on:click={() => chartData.data.datasets[0].data = [5, 2, 8, 10, 1, 7]}>Update Data</button>
```

In this example, the `chart` action initializes a Chart.js chart on a `<canvas>` element.  The `update` method updates the chart's data, and the `destroy` method destroys the chart instance to prevent memory leaks.

### Accessibility Considerations

When using actions to manipulate the DOM, always consider accessibility. Ensure that your actions do not negatively impact the user experience for people using assistive technologies.  For example, when using actions to manage focus, make sure that the focus order is logical and predictable.

### Resources

*   [Svelte Tutorial - Actions](https://svelte.dev/tutorial/actions): The official Svelte tutorial on actions.
*   [Svelte Documentation - Actions](https://svelte.dev/docs/svelte-action):  Detailed documentation on Svelte actions.

### Summary

Actions in Svelte provide a powerful way to interact directly with DOM elements, offering fine-grained control over their lifecycle. By understanding how to define, use, and manage actions effectively, you can enhance your Svelte components and integrate seamlessly with third-party libraries. Remember to consider accessibility and clean up resources to avoid potential issues. Experiment with actions to discover their full potential in your Svelte applications.