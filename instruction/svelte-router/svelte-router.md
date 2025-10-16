# Svelte Router

Svelte Router is a crucial component for building Single Page Applications (SPAs) with Svelte. It allows you to navigate between different views or components within your application without requiring full page reloads. This provides a smoother, more responsive user experience. Svelte itself doesn't come with a built-in router, so we'll explore different routing libraries, with a focus on `svelte-spa-router`, a popular and lightweight choice. We'll cover installation, configuration, basic routing, dynamic routes, and more. By the end of this guide, you'll be equipped to integrate routing into your Svelte projects effectively.

## Setting Up Svelte

Before diving into routing, ensure you have a Svelte project set up. You can use `degit` to quickly scaffold a new Svelte project:

```bash
npx degit sveltejs/template my-svelte-project
cd my-svelte-project
npm install
npm run dev
```

This creates a basic Svelte project in a directory called `my-svelte-project`, installs the necessary dependencies, and starts a development server.  You can access your application at `http://localhost:5000`.

## Choosing a Router

While several routing libraries exist for Svelte, `svelte-spa-router` is a common choice due to its simplicity and ease of use.  It's designed specifically for SPAs and integrates seamlessly with Svelte's component-based architecture.  Alternatives include `svelte-navigator` and `tinro`, each with their own strengths and weaknesses.  For this guide, we'll focus on `svelte-spa-router`.

## Installing `svelte-spa-router`

Install `svelte-spa-router` using npm:

```bash
npm install svelte-spa-router
```

## Basic Routing

The core concept behind `svelte-spa-router` is mapping URL paths to Svelte components.  This is achieved using a routes object and the `<Router>` component.

1.  **Create Route Components:**  Start by creating the Svelte components that will represent your different views (e.g., `Home.svelte`, `About.svelte`, `Contact.svelte`).

    *   `Home.svelte`:

        ```svelte
        <h1>Home Page</h1>
        <p>Welcome to the home page!</p>
        ```

    *   `About.svelte`:

        ```svelte
        <h1>About Us</h1>
        <p>Learn more about our company.</p>
        ```

    *   `Contact.svelte`:

        ```svelte
        <h1>Contact Us</h1>
        <p>Get in touch with us.</p>
        ```

2.  **Define Routes:**  In your main `App.svelte` file (or another appropriate component), import the `<Router>` component from `svelte-spa-router` and define your routes.

    ```svelte
    <script>
      import Router from 'svelte-spa-router';
      import Home from './Home.svelte';
      import About from './About.svelte';
      import Contact from './Contact.svelte';

      const routes = {
        '/': Home,
        '/about': About,
        '/contact': Contact,
      };
    </script>

    <Router {routes} />
    ```

3.  **Add Navigation Links:** Create navigation links that allow users to move between the different routes.  We can use standard HTML `<a>` elements for this.

    ```svelte
    <script>
      import Router from 'svelte-spa-router';
      import Home from './Home.svelte';
      import About from './About.svelte';
      import Contact from './Contact.svelte';

      const routes = {
        '/': Home,
        '/about': About,
        '/contact': Contact,
      };
    </script>

    <nav>
      <a href="/">Home</a> |
      <a href="/about">About</a> |
      <a href="/contact">Contact</a>
    </nav>

    <Router {routes} />
    ```

Now, when you click on the navigation links, the corresponding components will be rendered without a full page reload.

## Dynamic Routes

Dynamic routes allow you to create routes that include parameters in the URL, such as `/blog/:id`. This is particularly useful for displaying content based on a unique identifier.

1.  **Create a Dynamic Route Component:** Create a component that accepts a route parameter (e.g., `Blog.svelte`).

    ```svelte
    <script>
      export let id;
    </script>

    <h1>Blog Post</h1>
    <p>Post ID: {id}</p>
    ```

2.  **Define the Dynamic Route:**  In your `routes` object, define a route with a parameter using the `:parameterName` syntax.

    ```svelte
    <script>
      import Router from 'svelte-spa-router';
      import Home from './Home.svelte';
      import About from './About.svelte';
      import Contact from './Contact.svelte';
      import Blog from './Blog.svelte';

      const routes = {
        '/': Home,
        '/about': About,
        '/contact': Contact,
        '/blog/:id': Blog,
      };
    </script>

    <nav>
      <a href="/">Home</a> |
      <a href="/about">About</a> |
      <a href="/contact">Contact</a> |
      <a href="/blog/123">Blog Post 123</a>
    </nav>

    <Router {routes} />
    ```

Now, when you navigate to `/blog/123`, the `Blog` component will be rendered, and the `id` prop will be set to "123".

## Route Parameters and Props

`svelte-spa-router` automatically passes route parameters as props to the corresponding component. This is how the `Blog.svelte` component receives the `id` prop in the previous example.  You can access these props within your component and use them to fetch data or customize the view.

## Redirects

Redirects allow you to automatically navigate the user from one URL to another. This is useful for handling renamed pages or outdated URLs.  `svelte-spa-router` doesn't have built-in redirect functionality. However, you can achieve redirects using a simple component and conditional rendering.

```svelte
<script>
  import { navigate } from 'svelte-spa-router';
  import { onMount } from 'svelte';

  onMount(() => {
    navigate('/new-location', { replace: true });
  });
</script>

<p>Redirecting...</p>
```

This component, when rendered, will immediately redirect the user to `/new-location`. The `replace: true` option replaces the current entry in the browser's history, preventing the user from navigating back to the redirecting page.  You can then include this component in your routes:

```svelte
const routes = {
  '/old-location': RedirectComponent,
  '/new-location': NewLocationComponent
}
```

## Handling "Not Found" Pages

It's essential to handle cases where the user navigates to a URL that doesn't match any of your defined routes.  You can achieve this by defining a wildcard route (`*`) that renders a "Not Found" component.

1.  **Create a "Not Found" Component:** Create a component to display when a route is not found (e.g., `NotFound.svelte`).

    ```svelte
    <h1>404 Not Found</h1>
    <p>Sorry, the page you are looking for does not exist.</p>
    ```

2.  **Define the Wildcard Route:** Add a wildcard route (`*`) to your `routes` object, mapping it to the "Not Found" component.

    ```svelte
    <script>
      import Router from 'svelte-spa-router';
      import Home from './Home.svelte';
      import About from './About.svelte';
      import Contact from './Contact.svelte';
      import NotFound from './NotFound.svelte';

      const routes = {
        '/': Home,
        '/about': About,
        '/contact': Contact,
        '*': NotFound,
      };
    </script>

    <nav>
      <a href="/">Home</a> |
      <a href="/about">About</a> |
      <a href="/contact">Contact</a>
    </nav>

    <Router {routes} />
    ```

Now, if the user navigates to an undefined route, the `NotFound` component will be displayed.

## Common Challenges and Solutions

*   **Incorrect Route Matching:** Ensure your route definitions are precise and that you are using the correct syntax for dynamic routes. Double-check for typos in your route paths.
*   **Component Not Rendering:** Verify that your component is correctly imported and that the route is correctly mapped to the component in the `routes` object.  Inspect the browser's console for any errors.
*   **Route Parameters Not Passing:** Ensure you are exporting the route parameters as props in your component using `export let parameterName;`.  Also, confirm that the parameter name in the route definition (`/blog/:id`) matches the prop name in the component.
*   **Link Not Navigating:** Make sure your `<a>` tags have the `href` attribute set to the correct route path. Remember that `svelte-spa-router` handles client-side navigation, so you don't need to prevent the default link behavior.

## External Resources

*   **svelte-spa-router:** [https://github.com/EmilTholin/svelte-routing](https://github.com/EmilTholin/svelte-routing)
*   **Svelte Tutorial:** [https://svelte.dev/tutorial/basics](https://svelte.dev/tutorial/basics)

## Summary

You've learned how to implement routing in Svelte applications using `svelte-spa-router`. This included setting up the router, defining routes, creating dynamic routes, passing route parameters as props, handling redirects, and implementing "Not Found" pages. By mastering these concepts, you can build robust and user-friendly SPAs with Svelte. Practice implementing these techniques in your own projects to solidify your understanding and explore more advanced routing scenarios. Consider how you can apply these techniques to build more complex navigation patterns and enhance the user experience in your Svelte applications.