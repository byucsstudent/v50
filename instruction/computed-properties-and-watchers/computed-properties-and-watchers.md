# Computed Properties and Watchers

Computed properties and watchers are essential tools in Vue.js for managing and reacting to data changes within your application. They provide elegant ways to derive new data from existing data and execute side effects when data changes, respectively. Understanding these concepts is crucial for building reactive and maintainable Vue components.

Computed properties allow you to define properties that are dynamically calculated based on other reactive data. They act like regular properties, but their value is automatically updated whenever their dependencies change. Watchers, on the other hand, provide a more generic way to react to data changes, allowing you to execute custom code in response to specific data modifications.

## Computed Properties

Computed properties are functions that are cached based on their dependencies. Vue will only re-evaluate the computed property when one of its dependencies has changed. This caching mechanism provides significant performance benefits, especially when dealing with computationally intensive calculations.

To define a computed property, you add it to the `computed` option in your Vue component. The computed property should be a function that returns the desired value.

```javascript
Vue.component('computed-example', {
  data: function () {
    return {
      firstName: 'John',
      lastName: 'Doe'
    }
  },
  computed: {
    fullName: function () {
      return this.firstName + ' ' + this.lastName
    }
  },
  template: '<div>Full Name: {{ fullName }}</div>'
})
```

In this example, `fullName` is a computed property that depends on `firstName` and `lastName`. Whenever either `firstName` or `lastName` changes, `fullName` will be automatically updated.

**Getters and Setters**

Computed properties can also have getters and setters. The getter is used to retrieve the value of the computed property, while the setter is used to update the values of the data properties that the computed property depends on. This allows you to use computed properties for two-way data binding.

```javascript
Vue.component('computed-example-with-setter', {
  data: function () {
    return {
      firstName: 'John',
      lastName: 'Doe'
    }
  },
  computed: {
    fullName: {
      get: function () {
        return this.firstName + ' ' + this.lastName
      },
      set: function (newValue) {
        const names = newValue.split(' ')
        this.firstName = names[0]
        this.lastName = names[names.length - 1]
      }
    }
  },
  template: `
    <div>
      Full Name: {{ fullName }}
      <input v-model="fullName">
    </div>
  `
})
```

In this example, the `fullName` computed property has both a getter and a setter. When the input field is updated, the setter is called, which updates the `firstName` and `lastName` data properties accordingly.

**Practical Example: Filtering a List**

Consider a scenario where you have a list of items and you want to display only the items that match a certain criteria. You can use a computed property to filter the list.

```javascript
Vue.component('filtered-list', {
  data: function () {
    return {
      items: ['apple', 'banana', 'orange', 'grape'],
      filterText: ''
    }
  },
  computed: {
    filteredItems: function () {
      const filter = this.filterText.toLowerCase()
      return this.items.filter(item => item.toLowerCase().includes(filter))
    }
  },
  template: `
    <div>
      <input v-model="filterText" placeholder="Filter items">
      <ul>
        <li v-for="item in filteredItems" :key="item">{{ item }}</li>
      </ul>
    </div>
  `
})
```

In this example, `filteredItems` is a computed property that returns a filtered version of the `items` array based on the value of `filterText`. As the user types in the input field, the `filterText` data property is updated, which triggers the re-evaluation of the `filteredItems` computed property.

## Watchers

Watchers provide a more generic way to react to data changes. They allow you to execute custom code whenever a specific data property changes. Unlike computed properties, watchers are designed for performing side effects, such as making API calls or manipulating the DOM.

To define a watcher, you add it to the `watch` option in your Vue component. The watcher should be a function that takes two arguments: the new value of the data property and the old value of the data property.

```javascript
Vue.component('watcher-example', {
  data: function () {
    return {
      question: '',
      answer: 'Questions usually contain a question mark. ;)'
    }
  },
  watch: {
    // whenever question changes, this function will run
    question: function (newQuestion, oldQuestion) {
      console.log(`Asking question: ${newQuestion}`)
      this.getAnswer()
    }
  },
  methods: {
    getAnswer: _.debounce( // Note: Using lodash's debounce
      function () {
        if (this.question.indexOf('?') === -1) {
          this.answer = 'Questions usually contain a question mark. ;)'
          return
        }
        this.answer = 'Thinking...'
        axios
          .get('https://yesno.wtf/api')
          .then(response => {
            this.answer = _.capitalize(response.data.answer)
          })
          .catch(error => {
            this.answer = 'Error! Could not reach the API. ' + error
          })
      },
      500
    )
  },
  template: `
    <div>
      <p>
        Ask a yes/no question:
        <input v-model="question">
      </p>
      <p>{{ answer }}</p>
    </div>
  `
})
```

In this example, the watcher is triggered whenever the `question` data property changes. The watcher function then calls the `getAnswer` method, which makes an API call to retrieve a yes/no answer.  The `_.debounce` method from lodash is used to limit how often we send the API request, which is a common technique to improve performance and avoid excessive API calls.  You'll need to install lodash and axios to use this example `npm install lodash axios`.  The example also requires you to include lodash in your component.

**Deep Watchers**

By default, watchers only react to changes in the value of the data property itself. If you want to react to changes within a nested object or array, you need to use a deep watcher.

```javascript
Vue.component('deep-watcher-example', {
  data: function () {
    return {
      user: {
        name: 'John',
        age: 30
      }
    }
  },
  watch: {
    user: {
      handler: function (newVal, oldVal) {
        console.log('User object changed!')
      },
      deep: true
    }
  },
  template: '<div>User Name: {{ user.name }}</div>'
})
```

In this example, the `deep` option is set to `true`, which means that the watcher will be triggered whenever any property within the `user` object changes.

**Immediate Watchers**

By default, watchers are only triggered when the data property changes. If you want to trigger the watcher immediately when the component is created, you can use an immediate watcher.

```javascript
Vue.component('immediate-watcher-example', {
  data: function () {
    return {
      message: 'Hello'
    }
  },
  watch: {
    message: {
      handler: function (newVal, oldVal) {
        console.log('Message changed!')
      },
      immediate: true
    }
  },
  template: '<div>Message: {{ message }}</div>'
})
```

In this example, the `immediate` option is set to `true`, which means that the watcher will be triggered immediately when the component is created.

**Practical Example: Validating Input**

Watchers are often used for validating user input. For example, you can use a watcher to check if an email address is valid.

```javascript
Vue.component('validation-example', {
  data: function () {
    return {
      email: '',
      isValidEmail: true
    }
  },
  watch: {
    email: function (newEmail) {
      this.isValidEmail = this.validateEmail(newEmail)
    }
  },
  methods: {
    validateEmail: function (email) {
      const re = /^[^\s@]+@[^\s@]+\.[^\s@]+$/
      return re.test(email)
    }
  },
  template: `
    <div>
      <input v-model="email" placeholder="Enter your email">
      <p v-if="!isValidEmail" style="color: red;">Invalid email address</p>
    </div>
  `
})
```

In this example, the watcher is triggered whenever the `email` data property changes. The watcher function then calls the `validateEmail` method to check if the email address is valid. If the email address is invalid, the `isValidEmail` data property is set to `false`, which displays an error message.

## Common Challenges and Solutions

*   **Performance:** Overusing watchers or computed properties can impact performance. Make sure to use them judiciously and avoid complex calculations within them. Use caching where appropriate.
*   **Infinite Loops:** Be careful when using watchers to update the same data property that triggers the watcher. This can lead to infinite loops. Ensure that the watcher's logic has a clear exit condition or use computed properties instead.
*   **Debugging:** Debugging watchers can be tricky. Use `console.log` statements or Vue Devtools to track the values of the data properties and the execution of the watcher function.
*   **Asynchronous Operations:** When performing asynchronous operations within a watcher, be mindful of race conditions. Use techniques like debouncing or throttling to control the frequency of the operations.

## When to Use Computed Properties vs. Watchers

*   Use **computed properties** when you need to derive a new value from existing data and display it in the template. They are ideal for calculations and data transformations.
*   Use **watchers** when you need to perform side effects in response to data changes, such as making API calls, manipulating the DOM, or triggering other events.

## Summary

Computed properties and watchers are powerful tools for managing data and reactivity in Vue.js. Computed properties provide a way to derive new data from existing data, while watchers allow you to execute custom code in response to data changes. By understanding the differences between these concepts and using them appropriately, you can build more reactive, maintainable, and performant Vue applications. Remember to consider performance implications and potential pitfalls like infinite loops, and choose the right tool for the specific task at hand.