# Vue Router

Vue Router is the official router for Vue.js. It deeply integrates with Vue's core to make building Single Page Applications (SPAs) a breeze. SPAs offer a more fluid and responsive user experience compared to traditional multi-page applications, as they load a single HTML page and dynamically update the content as the user navigates. Vue Router allows you to define routes, map them to components, and manage navigation within your Vue application without full page reloads.

This content will guide you through the fundamentals of Vue Router, covering installation, basic configuration, route definitions, navigation, route parameters, named routes, programmatic navigation, and common challenges you might encounter. By the end, you'll be equipped to implement robust routing in your Vue projects.

## Installation

Before you can start using Vue Router, you need to install it in your Vue project. If you're using the Vue CLI, you can easily add Vue Router during project creation. If you have an existing project, you can install it using npm or yarn:

```bash
npm install vue-router@4
```

or

```bash
yarn add vue-router@4
```

The `@4` specifies that you're installing Vue Router version 4, which is compatible with Vue 3.

## Basic Configuration

Once installed, you need to configure Vue Router in your `main.js` file (or equivalent entry point). This involves importing `createRouter` and `createWebHistory` (or `createWebHashHistory`), defining your routes, and mounting the router instance to your Vue application.

Here's a basic example:

```javascript
// main.js
import { createApp } from 'vue'
import { createRouter, createWebHistory } from 'vue-router'
import App from './App.vue'
import Home from './components/Home.vue'
import About from './components/About.vue'

// 1. Define route components.
// These can be imported from other files
const routes = [
  { path: '/', component: Home },
  { path: '/about', component: About }
]

// 2. Define some routes
// Each route should map to a component.
// We'll talk about nested routes later.

// 3. Create the router instance and pass the `routes` option
// You can pass in additional options here, but let's
// keep it simple for now.
const router = createRouter({
  history: createWebHistory(),
  routes // short for `routes: routes`
})

// 4. Create and mount the app instance.
const app = createApp(App)
// Make sure to _use_ the router instance to make the
// whole app router-aware.
app.use(router)

app.mount('#app')
```

In this example:

*   We import `createRouter` and `createWebHistory` from `vue-router`.
*   We define an array of `routes`, where each route is an object with a `path` and a `component`. The `path` specifies the URL route, and the `component` is the Vue component that should be rendered when that route is active.
*   `createWebHistory()` configures the router to use HTML5 history mode, which provides clean URLs without hash symbols (`#`).  Alternatively, you can use `createWebHashHistory()` which uses hash-based URLs.  `createWebHistory` requires proper server configuration to function correctly.
*   We create a router instance using `createRouter`, passing in the `history` mode and the `routes` array.
*   Finally, we use `app.use(router)` to install the router in our Vue application.

## Defining Routes

Routes are defined as an array of objects, where each object represents a single route. Each route object must have a `path` property, which specifies the URL route, and a `component` property, which specifies the Vue component to render.

```javascript
const routes = [
  { path: '/', component: Home },
  { path: '/about', component: About },
  { path: '/contact', component: Contact }
]
```

Here, `/` maps to the `Home` component, `/about` maps to the `About` component, and `/contact` maps to the `Contact` component.

## Navigation

Vue Router provides two primary ways to navigate between routes: using the `<router-link>` component and programmatic navigation.

### Using `<router-link>`

The `<router-link>` component is the preferred way to navigate between routes in your templates.  It renders a link with the correct `href` attribute and automatically handles preventing page reloads.

```vue
<template>
  <nav>
    <router-link to="/">Home</router-link> |
    <router-link to="/about">About</router-link> |
    <router-link to="/contact">Contact</router-link>
  </nav>

  <router-view></router-view>
</template>
```

The `to` prop specifies the target route.  Vue Router will automatically generate the correct URL. The `<router-view>` component is where the matched component will be rendered.

### Programmatic Navigation

Sometimes you need to navigate programmatically, such as after a form submission or in response to a user action.  You can access the router instance through `$router` and use its methods like `push` and `replace`.

```vue
<template>
  <button @click="goToAbout">Go to About Page</button>
</template>

<script>
import { useRouter } from 'vue-router';

export default {
  setup() {
    const router = useRouter();

    const goToAbout = () => {
      router.push('/about');
    };

    return {
      goToAbout,
    };
  },
};
</script>
```

`router.push()` navigates to the specified route, adding a new entry to the history stack. `router.replace()` navigates to the specified route, replacing the current entry in the history stack.  The latter prevents the user from going back to the previous page using the browser's back button.

## Route Parameters

Route parameters allow you to create dynamic routes that can accept values in the URL.  This is useful for displaying details about specific items, such as a product page or a blog post.

```javascript
const routes = [
  { path: '/product/:id', component: ProductDetail }
]
```

In this example, `:id` is a route parameter.  When a user navigates to `/product/123`, the `ProductDetail` component will be rendered, and the value `123` will be available as the `id` parameter.

You can access route parameters using the `$route.params` object:

```vue
<template>
  <h1>Product ID: {{ productId }}</h1>
</template>

<script>
import { useRoute } from 'vue-router';

export default {
  setup() {
    const route = useRoute();
    const productId = route.params.id;

    return {
      productId,
    };
  },
};
</script>
```

## Named Routes

Named routes provide a way to refer to routes by name instead of by path.  This can make your code more readable and maintainable, especially when dealing with complex routes.

```javascript
const routes = [
  { path: '/product/:id', name: 'product', component: ProductDetail }
]
```

You can then use the `name` property in `<router-link>` or in programmatic navigation:

```vue
<router-link :to="{ name: 'product', params: { id: 123 } }">Product 123</router-link>
```

```javascript
router.push({ name: 'product', params: { id: 456 } });
```

## Common Challenges and Solutions

*   **404 Errors on Page Refresh:** When using `createWebHistory` mode, you need to configure your server to properly handle routes.  The server needs to be configured to serve your `index.html` file for all routes, allowing Vue Router to handle the client-side routing.  Consult your server's documentation for specific instructions.

*   **Passing Props to Components:**  You can pass props to route components using the `props` option in the route definition. This can be useful for passing data directly to the component instead of relying on `$route.params`.

    ```javascript
    const routes = [
      {
        path: '/user/:id',
        component: User,
        props: true // Route params will be passed as props
      }
    ]
    ```

    In this case, the `id` parameter will be passed as a prop to the `User` component.

*   **Nested Routes:** Vue Router supports nested routes, allowing you to create hierarchical navigation structures. Define `children` routes within a parent route to create nested views.  Remember to include a `<router-view>` within the parent component to render the child routes.

*   **Route Guards:** Route guards allow you to control access to certain routes based on conditions such as authentication status or permissions.  You can use `beforeEach`, `beforeResolve`, and `afterEach` navigation guards to execute code before or after a route is accessed.  `beforeEnter` guards are also possible and can be defined on a per-route basis.

## Summary

Vue Router is a powerful and essential tool for building SPAs with Vue.js. It enables you to define routes, map them to components, and manage navigation within your application. By understanding the fundamentals of installation, configuration, route definitions, navigation, route parameters, and named routes, you can create robust and user-friendly routing experiences. Remember to consult the official Vue Router documentation for more advanced features and customization options.  Experiment with the provided examples and consider building small projects to solidify your understanding.

## Further Resources

*   [Official Vue Router Documentation](https://router.vuejs.org/)
*   [Vue.js Official Guide](https://vuejs.org/guide/introduction.html)