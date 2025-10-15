# Svelte Router

Svelte Router is a crucial component for building Single Page Applications (SPAs) with Svelte. It allows you to manage different views or pages within your application without requiring full page reloads, resulting in a smoother and faster user experience. This content will guide you through understanding and implementing routing in your Svelte applications. We will cover the fundamental concepts, explore different routing libraries, and demonstrate practical examples to get you started.

Routing is the mechanism that enables navigation between different sections or "pages" of your application. In traditional web applications, each navigation action triggers a request to the server, which then sends back a new HTML page. SPAs, on the other hand, handle routing on the client-side using JavaScript. This means that when a user clicks a link, JavaScript intercepts the request, updates the URL in the browser's address bar, and renders the appropriate component without reloading the entire page.

## Choosing a Svelte Router Library

Several Svelte router libraries are available, each offering different features and approaches. Some popular choices include:

*   **svelte-routing:** A lightweight and relatively simple router that utilizes the browser's history API. It's a good choice for smaller projects or when you need a minimal solution.
*   **@sveltejs/kit:** SvelteKit is a full-fledged framework built on top of Svelte, and it includes a powerful built-in router.  If you're starting a new project, SvelteKit is generally the recommended approach.
*   **routify:** A file-based router that automatically generates routes based on your directory structure. This can simplify routing configuration, especially for larger applications.

For this content, we'll primarily focus on `svelte-routing` due to its ease of use and suitability for demonstrating core routing concepts. However, the principles discussed are applicable to other routers as well. Remember to check the documentation of the router you choose.

## Installing svelte-routing

To install `svelte-routing`, use npm or yarn:

```bash
npm install svelte-routing
# or
yarn add svelte-routing
```

## Basic Routing with svelte-routing

Let's create a simple application with two routes: a home page (`/`) and an about page (`/about`).

First, create a file named `App.svelte`:

```svelte
<script>
  import { Router, Route, Link } from 'svelte-routing';

  import Home from './Home.svelte';
  import About from './About.svelte';
  import NotFound from './NotFound.svelte';
</script>

<Router>
  <nav>
    <Link to="/">Home</Link>
    <Link to="/about">About</Link>
  </nav>

  <Route path="/">
    <Home />
  </Route>
  <Route path="/about">
    <About />
  </Route>
  <Route path="*">
    <NotFound />
  </Route>
</Router>

<style>
  nav {
    margin-bottom: 20px;
  }

  a {
    margin-right: 10px;
  }
</style>
```

Next, create the `Home.svelte` component:

```svelte
<h1>Home Page</h1>
<p>Welcome to the home page!</p>
```

Then, create the `About.svelte` component:

```svelte
<h1>About Page</h1>
<p>Learn more about us!</p>
```

Finally, create a `NotFound.svelte` component to handle routes that don't match:

```svelte
<h1>404 Not Found</h1>
<p>Sorry, the page you are looking for does not exist.</p>
```

In this example:

*   We import the `Router`, `Route`, and `Link` components from `svelte-routing`.
*   The `Router` component wraps the entire routing configuration.
*   The `Link` component creates navigation links that update the URL without reloading the page.
*   The `Route` component associates a path with a specific component. When the current URL matches the path, the corresponding component is rendered.
*   The `path="*" ` in the last route acts as a catch-all for any unmatched routes, displaying the `NotFound` component.

## Route Parameters

Often, you'll need to pass parameters in your routes, such as an ID for a specific item. `svelte-routing` supports route parameters using dynamic segments in the path.

Create a `User.svelte` component:

```svelte
<script>
  import { getContext } from 'svelte';

  const { params } = getContext('svelte-routing');

  $: userId = $params.id;
</script>

<h1>User Profile</h1>
<p>User ID: {userId}</p>
```

Update the `App.svelte` component:

```svelte
<script>
  import { Router, Route, Link } from 'svelte-routing';

  import Home from './Home.svelte';
  import About from './About.svelte';
  import User from './User.svelte';
  import NotFound from './NotFound.svelte';
</script>

<Router>
  <nav>
    <Link to="/">Home</Link>
    <Link to="/about">About</Link>
    <Link to="/user/123">User 123</Link>
  </nav>

  <Route path="/">
    <Home />
  </Route>
  <Route path="/about">
    <About />
  </Route>
  <Route path="/user/:id">
    <User />
  </Route>
  <Route path="*">
    <NotFound />
  </Route>
</Router>

<style>
  nav {
    margin-bottom: 20px;
  }

  a {
    margin-right: 10px;
  }
</style>
```

In this example:

*   The route `/user/:id` defines a dynamic segment `:id`.
*   The `User` component accesses the value of `id` from the `params` context using `getContext('svelte-routing')`.  The `$params` variable is a Svelte store that automatically updates when the route changes.

## Navigating Programmatically

Sometimes you need to navigate programmatically, such as after a form submission or based on some other application logic. `svelte-routing` provides the `push` function for this purpose.

```svelte
<script>
  import { push } from 'svelte-routing';

  function handleSubmit() {
    // Perform form submission logic here
    push('/about'); // Navigate to the about page
  }
</script>

<form on:submit|preventDefault={handleSubmit}>
  <button type="submit">Submit</button>
</form>
```

## Common Challenges and Solutions

*   **Incorrect Route Matching:** Double-check your route paths for typos and ensure they are specific enough to avoid conflicts. Use the `*` route carefully to handle truly unmatched routes.
*   **Accessing Route Parameters:**  Make sure you are correctly accessing the route parameters using `getContext('svelte-routing')` and subscribing to the `$params` store.
*   **Link Not Working:** Ensure the `Link` component is used within a `Router` component.

## SvelteKit Routing

SvelteKit offers a powerful file-based routing system. Every file in your `src/routes` directory becomes a route.

* `src/routes/index.svelte` becomes the `/` route.
* `src/routes/about.svelte` becomes the `/about` route.
* `src/routes/blog/[slug].svelte` becomes the `/blog/:slug` route, where `[slug]` is a dynamic parameter.

SvelteKit also provides features like layouts, hooks, and server-side rendering, making it a more complete solution for building complex Svelte applications.

## External Resources

*   **svelte-routing:** [https://github.com/EmilTholin/svelte-routing](https://github.com/EmilTholin/svelte-routing)
*   **SvelteKit Routing:** [https://kit.svelte.dev/docs/routing](https://kit.svelte.dev/docs/routing)

## Summary

This content has provided a foundation for understanding and implementing routing in Svelte applications. You've learned how to choose a router library, configure basic routes, handle route parameters, navigate programmatically, and address common challenges. You've also had a brief introduction to SvelteKit's file-based routing. Experiment with these concepts to build dynamic and engaging SPAs with Svelte.  Consider building a small application that utilizes multiple routes and route parameters. How would you handle nested routes or more complex navigation scenarios?