# Filters

Vue filters are a powerful tool for formatting data displayed in your templates. They allow you to transform raw data into a user-friendly format without altering the underlying data itself. Think of them as little functions you can chain onto expressions in your templates to modify their output. Filters promote code reusability and keep your components cleaner by separating formatting logic from component logic.

## What are Vue Filters?

Filters are essentially functions that take a value as input and return a modified version of that value. They're designed for simple data transformations like formatting dates, currency, text capitalization, and much more. They can be used in two places:

*   **Mustache interpolations (double curly braces):** `{{ message | capitalize }}`
*   **`v-bind` expressions:** `<div v-bind:id="rawId | formatId"></div>`

## Defining Filters

You can define filters globally or locally within a component.

### Global Filters

Global filters are accessible throughout your entire Vue application. You define them using `Vue.filter()`.

```javascript
Vue.filter('capitalize', function (value) {
  if (!value) return ''
  value = value.toString()
  return value.charAt(0).toUpperCase() + value.slice(1)
})

new Vue({
  el: '#app',
  data: {
    message: 'hello world'
  }
})
```

In this example, we've defined a global filter called `capitalize`.  It takes a string as input, capitalizes the first letter, and returns the modified string.  Any component in your application can now use this filter.

HTML:

```html
<div id="app">
  <p>{{ message | capitalize }}</p>
</div>
```

This will render:

```html
<p>Hello world</p>
```

### Local Filters

Local filters are specific to a component. You define them within the `filters` option of a component.

```javascript
new Vue({
  el: '#app',
  data: {
    message: 'hello world'
  },
  filters: {
    lowercase: function (value) {
      if (!value) return ''
      value = value.toString()
      return value.toLowerCase()
    }
  }
})
```

Here, we've defined a local filter called `lowercase` that converts a string to lowercase. This filter is only available within this specific Vue instance.

HTML:

```html
<div id="app">
  <p>{{ message | lowercase }}</p>
</div>
```

This will render:

```html
<p>hello world</p>
```

Local filters take precedence over global filters if they have the same name.

## Chaining Filters

Filters can be chained together, allowing you to apply multiple transformations to a value. The output of one filter becomes the input of the next.

```html
<p>{{ message | capitalize | lowercase }}</p>
```

In this example, we first capitalize the `message` using the `capitalize` filter, then convert the result to lowercase using the `lowercase` filter.  Filters are applied in the order they appear from left to right.

## Passing Arguments to Filters

Filters can accept arguments in addition to the value being transformed. These arguments are passed after the filter name, separated by spaces.

```javascript
Vue.filter('truncate', function (value, length) {
  if (!value) return ''
  value = value.toString()
  if (value.length <= length) {
    return value
  }
  return value.substring(0, length) + '...'
})

new Vue({
  el: '#app',
  data: {
    longText: 'This is a very long text that needs to be truncated.'
  }
})
```

HTML:

```html
<div id="app">
  <p>{{ longText | truncate(20) }}</p>
</div>
```

This will render:

```html
<p>This is a very long...</p>
```

In this case, `truncate` is a global filter that accepts the initial `value` and a `length` argument. The number `20` is passed as the `length` argument to the filter.

## Practical Examples

Here are some more practical examples of how you can use Vue filters:

*   **Formatting Currency:**

    ```javascript
    Vue.filter('currency', function (value) {
      const formatter = new Intl.NumberFormat('en-US', {
        style: 'currency',
        currency: 'USD'
      })
      return formatter.format(value)
    })
    ```

    HTML:

    ```html
    <p>Price: {{ price | currency }}</p>
    ```

*   **Formatting Dates:**

    ```javascript
    Vue.filter('date', function (value) {
      return new Date(value).toLocaleDateString()
    })
    ```

    HTML:

    ```html
    <p>Published Date: {{ publishDate | date }}</p>
    ```

*   **Converting to Uppercase:**

    ```javascript
    Vue.filter('uppercase', function (value) {
      if (!value) return ''
      value = value.toString()
      return value.toUpperCase()
    })
    ```

    HTML:

    ```html
    <p>Name: {{ name | uppercase }}</p>
    ```

## Common Challenges and Solutions

*   **Filter Not Working:**  Double-check the filter name in your template and ensure it matches the name you defined in either the global or local filters. Verify the value being passed to the filter is of the expected type.  Use your browser's developer console to inspect the data and look for any errors.
*   **Filter Arguments Not Being Passed Correctly:**  Ensure you're passing arguments to the filter with the correct syntax:  `{{ value | filterName(arg1, arg2) }}`.  Also, verify the filter function correctly handles the arguments you're passing.
*   **Performance Issues:**  Avoid performing complex or computationally expensive operations within filters, as they are executed frequently during rendering. If you need to perform complex transformations, consider doing it within a computed property instead.
*   **Conflicting Filter Names:**  If you have both global and local filters with the same name, the local filter will always take precedence within the component where it's defined. Be mindful of naming conventions to avoid conflicts.

## Alternatives to Filters

While filters are a convenient feature, there are alternatives you should consider, especially for more complex formatting logic.

*   **Computed Properties:** Computed properties are reactive and can perform more complex calculations and formatting. They're generally preferred over filters for anything beyond simple transformations.
*   **Methods:** Methods can also be used for formatting data, but they are not reactive like computed properties. Use methods when the formatting logic involves side effects or requires manual triggering.
*   **External Libraries:**  Libraries like Moment.js (for date formatting) or Numeral.js (for number formatting) provide robust and feature-rich solutions for complex formatting requirements.

## Summary

Vue filters offer a simple and effective way to format data within your templates. You can define them globally for application-wide use or locally within components.  They are useful for simple transformations like capitalization, truncation, or basic currency/date formatting.  However, for more complex formatting logic or performance-critical scenarios, computed properties, methods, or external libraries might be a better choice.  Experiment with filters and the alternatives to determine the best approach for your specific needs. Always prioritize readability and maintainability when choosing a formatting strategy.