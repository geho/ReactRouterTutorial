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

## Data Writes + HTML Forms

https://reactrouter.com/en/main/start/tutorial#data-writes--html-forms

React Router emulates HTML Form navigation as the data mutation primitive, according to web development before the JavaScript cambrian explosion. It gives you the UX capabilities of client rendered apps with the simplicity of the "old school" web model.

While unfamiliar to some web developers, HTML forms actually cause a navigation in the browser, just like clicking a link. The only difference is in the request: links can only change the URL while forms can also change the request method (GET vs POST) and the request body (POST form data).

Without client side routing, the browser will serialize the form's data automatically and send it to the server as the request body for POST, and as URLSearchParams for GET. React Router does the same thing, except instead of sending the request to the server, it uses client side routing and sends it to a route `action`.

We can test this out by clicking the "New" button in our app. The app should blow up because the Vite server isn't configured to handle a POST request (it sends a 404, though it should probably be a 405 🤷).

Instead of sending that POST to the Vite server to create a new contact, let's use client side routing instead.

> [!NOTE]  
> You can open the network tab in the browser devtools to see that it's returning 404
> if the Browser screen keeps empty and does not show the 404 error directly.

## Creating Contacts

https://reactrouter.com/en/main/start/tutorial#creating-contacts

We'll create new contacts by exporting an action in our root route, wiring it up to the route config, and changing our `<form>` to a React Router `<Form>`.

- Create the action `async function action()` and change `<form>` to `<Form>` at `<div id="sidebar">` in `src/routes/root.jsx`
- Import and set the action on the route in `src/main.jsx`

The createContact method just creates an empty contact with no name or data or anything. But it does still create a record, promise!

> 🧐 Wait a sec ... How did the sidebar update? Where did we call the `action`?
> Where's the code to refetch the data? Where are `useState`, `onSubmit` and `useEffect`?!

This is where the "old school web" programming model shows up. As we discussed earlier, `<Form>` prevents the browser from sending the request to the server and sends it to your route action instead. In web semantics, a POST usually means some data is changing. By convention, React Router uses this as a hint to automatically revalidate the data on the page after the action finishes. That means all of your `useLoaderData` hooks update and the UI stays in sync with your data automatically! Pretty cool.

## URL Params in Loaders

https://reactrouter.com/en/main/start/tutorial#url-params-in-loaders

- Click on the No Name record

We should be seeing our old static contact page again, with one difference: the URL now has a real ID for the record.

Reviewing the route config, the route looks like this:

```jsx
[
  {
    path: "contacts/:contactId",
    element: <Contact />,
  },
];
```

> [!NOTE]  
> Note the `:contactId` URL segment. The colon (`:`) has special meaning, turning it into a "dynamic segment".
> Dynamic segments will match dynamic (changing) values in that position of the URL, like the contact ID.
> We call these values in the URL "URL Params", or just "params" for short.

These `params` are passed to the loader with keys that match the dynamic segment. For example, our segment is named `:contactId` so the value will be passed as `params.contactId`.

These params are most often used to find a record by ID.

- Add a loader `async function loader({ params })` to the contact page and access data with `useLoaderData()` in `src/routes/contact.jsx`
- Configure the loader on the route in `src/main.jsx`

## Updating Data

https://reactrouter.com/en/main/start/tutorial#updating-data

Just like creating data, you update data with `<Form>`. Let's make a new route at `contacts/:contactId/edit`. Again, we'll start with the component and then wire it up to the route config.

- Add the edit page UI in `src/routes/edit.jsx`
- Add the new edit route in `src/main.jsx`

We want it to be rendered in the root route's outlet, so we made it a sibling to the existing child route.

(You might note we reused the `contactLoader` for this route. This is only because we're being lazy in the tutorial. There is no reason to attempt to share loaders among routes, they usually have their own.)

Alright, clicking the "Edit" button gives us this new UI.

## Updating Contacts with FormData

https://reactrouter.com/en/main/start/tutorial#updating-contacts-with-formdata

The edit route we just created already renders a form. All we need to do to update the record is wire up an action to the route. The form will post to the action and the data will be automatically revalidated.

- Add an action to the edit module in `src/routes/edit.jsx`
- Wire the action up to the route in `src/main.jsx`

## Mutation Discussion

https://reactrouter.com/en/main/start/tutorial#mutation-discussion

> 😑 It worked, but I have no idea what is going on here...

Let's dig in a bit...

Open up `src/routes/edit.jsx` and look at the form elements. Notice how they each have a name:

```jsx
<input
  placeholder="First"
  aria-label="First name"
  type="text"
  name="first"
  defaultValue={contact.first}
/>
```

```jsx
name = "first";
```

Without JavaScript, when a form is submitted, the browser will create `FormData` and set it as the body of the request when it sends it to the server. As mentioned before, React Router prevents that and sends the request to your action instead, including the `FormData`.

Each field in the form is accessible with `formData.get(name)`. For example, given the input field from above, you could access the first and last names like this:

```jsx
export async function action({ request, params }) {
  const formData = await request.formData();
  const firstName = formData.get("first");
  const lastName = formData.get("last");
  // ...
}
```

```jsx
const firstName = formData.get("first");
const lastName = formData.get("last");
```

Since we have a handful of form fields, we used `Object.fromEntries()` to collect them all into an object, which is exactly what our `updateContact()` function wants.

```jsx
const updates = Object.fromEntries(formData);
updates.first; // "Some"
updates.last; // "Name"
```

Aside from `action()`, none of these APIs we're discussing are provided by React Router: `request`, `request.formData()`, `Object.fromEntries()` are all provided by the web platform.

After we finished the action, note the `redirect()` at the end:

```jsx
export async function action({ request, params }) {
  const formData = await request.formData();
  const updates = Object.fromEntries(formData);
  await updateContact(params.contactId, updates);
  return redirect(`/contacts/${params.contactId}`);
}
```

Loaders and actions can both _return a `Response`_ (makes sense, since they received a _`Request`_!). The `redirect()` helper just makes it easier to return a _response_ that tells the app to change locations.

Without client side routing, if a server redirected after a POST request, the new page would fetch the latest data and render. As we learned before, React Router emulates this model and automatically revalidates the data on the page after the action. That's why the sidebar automatically updates when we save the form. The extra revalidation code doesn't exist without client side routing, so it doesn't need to exist with client side routing either!
