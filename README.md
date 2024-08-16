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
