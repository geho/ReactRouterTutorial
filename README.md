# React Router Tutorial

https://reactrouter.com/en/main/start/tutorial

## Setup

https://reactrouter.com/en/main/start/tutorial#setup

```shell
npm create vite@latest ReactRouterTutorial -- --template react
# follow prompts
cd ReactRouterTutorial
npm install react-router-dom # always need this!
npm install localforage match-sorter sort-by # only for this tutorial.
(
cat <<EOT
// React Router Tutorial
import { StrictMode } from "react";
import { createRoot } from "react-dom/client";
import "./index.css";

createRoot(document.getElementById("root")).render(
  <StrictMode>
    <h1>React Router Tutorial</h1>
  </StrictMode>
);
EOT
) > src/main.jsx
rm -f src/App.*
rm -f src/index.css
wget -O src/index.css -N https://gist.githubusercontent.com/ryanflorence/ba20d473ef59e1965543fa013ae4163f/raw/499707f25a5690d490c7b3d54c65c65eb895930c/react-router-6.4-tutorial-css.css
wget -O src/contacts.js -N https://gist.githubusercontent.com/ryanflorence/1e7f5d3344c0db4a8394292c157cd305/raw/f7ff21e9ae7ffd55bfaaaf320e09c6a08a8a6611/contacts.js
echo -e "// React Router Tutorial" > src/error-page.jsx
mkdir src/routes
echo -e "// React Router Tutorial" > src/routes/root.jsx
echo -e "// React Router Tutorial" > src/routes/index.jsx
echo -e "// React Router Tutorial" > src/routes/contact.jsx
echo -e "// React Router Tutorial" > src/routes/edit.jsx
echo -e "// React Router Tutorial" > src/routes/destroy.jsx
npx vite --host 0.0.0.0
```

## Adding a Router

https://reactrouter.com/en/main/start/tutorial#adding-a-router

- create a `BrowserRouter` in `src/main.jsx` ("root route")

## The Root Route

https://reactrouter.com/en/main/start/tutorial#the-root-route

- Create the root layout component `Root` in `src/routes/root.jsx`
- Set `<Root>` as the root route's `element` in `src/main.jsx`

## Handling Not Found Errors

https://reactrouter.com/en/main/start/tutorial#handling-not-found-errors

- Create an error page component `ErrorPage` in `src/error-page.jsx`
- Set the `<ErrorPage>` as the `errorElement` on the root route in `src/main.jsx`

> [!NOTE]  
> `useRouteError()` provides the error that was thrown.
>
> When the user navigates to routes that don't exist you'll get an error response with a "Not Found" `statusText`.

## The Contact Route UI

https://reactrouter.com/en/main/start/tutorial#the-contact-route-ui

- Add the contact component UI `Contact` in `src/routes/contact.jsx`
- Import the contact component `Contact`and create a new route in `src/main.jsx`

> [!NOTE]  
> However, it's not inside of our root layout 😠

## Nested Routes

https://reactrouter.com/en/main/start/tutorial#nested-routes

We want the contact component to render inside of the `<Root>` layout.

We do it by making the contact route a _child_ of the root route.

- Move the contacts route to be a _child_ of the root route in `src/main.jsx`
- Render an `<Outlet>` at `<div id="detail">` in `src/routes/root.jsx`

> [!NOTE]  
> Without an `<Outlet>` we'll see the root layout again but a blank page on the right.

## Client Side Routing

https://reactrouter.com/en/main/start/tutorial#client-side-routing

You may or may not have noticed, but when we click the links in the sidebar, the browser is doing a full document request for the next URL instead of using React Router.

Client side routing allows our app to update the URL without requesting another document from the server. Instead, the app can immediately render new UI. Let's make it happen with `<Link>`.

- Change the sidebar (at `<div id="sidebar">`) `<a href>` to `<Link to>` in `src/routes/root.jsx`

> [!NOTE]  
> You can open the network tab in the browser devtools to see that it's not requesting documents anymore.

## Loading Data

https://reactrouter.com/en/main/start/tutorial#loading-data

URL segments, layouts, and data are more often than not coupled (tripled?) together. We can see it in this app already:

| URL Segment    | Component   | Data               |
| -------------- | ----------- | ------------------ |
| `/`            | `<Root>`    | list of contacts   |
| `contacts/:id` | `<Contact>` | individual contact |

Because of this natural coupling, React Router has data conventions to get data into your route components easily.

There are two APIs we'll be using to load data, `loader()` and `useLoaderData()`.

1. First we'll create and export a loader function in the root module.
2. Then we'll hook it up to the route.
3. Finally, we'll access and render the data.

- Export a loader `async function loader()` from `src/routes/root.jsx`
- Configure the loader on the route in `src/main.jsx`
- Access and render the data at `<div id="sidebar">` inbetween `<nav>` and `</nav>` in `src/routes/root.jsx`

That's it! React Router will now automatically keep that data in sync with your UI.
