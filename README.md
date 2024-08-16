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

## Redirecting new records to the edit page

https://reactrouter.com/en/main/start/tutorial#redirecting-new-records-to-the-edit-page

Now that we know how to redirect, let's update the action that creates new contacts to redirect to the edit page:

- Redirect to the new record's edit page in `async function action()` in `src/routes/root.jsx`

## Active Link Styling

https://reactrouter.com/en/main/start/tutorial#active-link-styling

Now that we have a bunch of records, it's not clear which one we're looking at in the sidebar. We can use `NavLink` to fix this.

- Use a `NavLink` in the sidebar at `<div id="sidebar">` inbetween `<nav>` and `</nav>` in `src/routes/root.jsx`

> [!NOTE]  
> Note that we are passing a function to `className`.
> When the user is at the URL in the `NavLink`, then `isActive` will be true.
> When it's about to be active (the data is still loading) then `isPending` will be true.
> This allows us to easily indicate where the user is, as well as provide immediate feedback
> on links that have been clicked but we're still waiting for data to load.

## Global Pending UI

https://reactrouter.com/en/main/start/tutorial#global-pending-ui

As the user navigates the app, React Router will _leave the old page up_ as data is loading for the next page. You may have noticed the app feels a little unresponsive as you click between the list. Let's provide the user with some feedback so the app doesn't feel unresponsive.

React Router is managing all of the state behind the scenes and reveals the pieces of it you need to build dynamic web apps. In this case, we'll use the `useNavigation()` hook.

- `useNavigation()` to add global pending UI at `<div id="detail">` in `src/routes/root.jsx`

_`useNavigation`_ returns the current navigation state: it can be one of `"idle" | "submitting" | "loading"`.

In our case, we add a `"loading"` class to the main part of the app if we're not idle. The CSS then adds a nice fade after a short delay (to avoid flickering the UI for fast loads). You could do anything you want though, like show a spinner or loading bar across the top.

> [!NOTE]  
> Note that our data model (`src/contacts.js`) has a clientside cache,
> so navigating to the same contact is fast the second time.
> This behavior is not React Router, it will re-load data for changing routes
> no matter if you've been there before or not.
> It does, however, avoid calling the loaders for _unchanging_ routes (like the list)
> during a navigation.

## Deleting Records

https://reactrouter.com/en/main/start/tutorial#deleting-records

If we review code in the contact route (`src/routes/contact.jsx`), we can find the delete button looks like this:

```jsx
<Form
  method="post"
  action="destroy"
  onSubmit={(event) => {
    if (!confirm("Please confirm you want to delete this record.")) {
      event.preventDefault();
    }
  }}>
  <button type="submit">Delete</button>
</Form>
```

```jsx
action = "destroy";
```

Note the `action` points to `"destroy"`. Like `<Link to>`, `<Form action>` can take a _relative_ value. Since the form is rendered in `contact/:contactId`, then a relative action with `destroy` will submit the form to `contact/:contactId/destroy` when clicked.

At this point you should know everything you need to know to make the delete button work. You'll need:

1.  A new route
2.  An `action` at that route
3.  `deleteContact()` from `src/contacts.js`

- Add the destroy action ` async function action({ params })` in `src/routes/destroy.jsx`
- Add the destroy route to the route config in `src/main.jsx`

Alright, navigate to a record and click the "Delete" button. It works!

> 😅 I'm still confused why this all works

When the user clicks the submit button:

1.  `<Form>` prevents the default browser behavior of sending a new POST request to the server, but instead emulates the browser by creating a POST request with client side routing
2.  The `<Form action="destroy">` matches the new route at `"contacts/:contactId/destroy"` and sends it the request
3.  After the action redirects, React Router calls all of the loaders for the data on the page to get the latest values (this is "revalidation"). `useLoaderData()` returns new values and causes the components to update!

Add a form, add an action, React Router does the rest.

## Contextual Errors

https://reactrouter.com/en/main/start/tutorial#contextual-errors

Just for kicks, throw an error in the destroy action in `src/routes/destroy.jsx`:

```jsx
export async function action({ params }) {
  throw new Error("oh dang!");
  await deleteContact(params.contactId);
  return redirect("/");
}
```

```jsx
throw new Error("oh dang!");
```

Recognize that screen? It's our `errorElement` from before. The user, however, can't really do anything to recover from this screen except to hit refresh.

- Let's create a contextual error message for the destroy route in `src/main.jsx`

Now try it again.

Our user now has more options than slamming refresh, they can continue to interact with the parts of the page that aren't having trouble 🙌

Because the destroy route has its own `errorElement` and is a child of the root route, the error will render there instead of the root. As you probably noticed, these errors bubble up to the nearest `errorElement`. Add as many or as few as you like, as long as you've got one at the root.

## Index Routes

https://reactrouter.com/en/main/start/tutorial#index-routes

When we load up the app, you'll notice a big blank page on the right side of our list.

When a route has children, and you're at the parent route's path, the `<Outlet>` has nothing to render because no children match. You can think of index routes as the default child route to fill in that space.

- Fill in the index component's elements in `src/routes/index.jsx`
- Configure the index route in `src/main.jsx`

Note the `{ index:true }` instead of `{ path: "" }`. That tells the router to match and render this route when the user is at the parent route's exact path, so there are no other child routes to render in the `<Outlet>`.

Voila! No more blank space. It's common to put dashboards, stats, feeds, etc. at index routes. They can participate in data loading as well.

## Cancel Button

https://reactrouter.com/en/main/start/tutorial#cancel-button

On the edit page we've got a cancel button that doesn't do anything yet. We'd like it to do the same thing as the browser's back button.

We'll need a click handler `onClick()` on the button as well as `useNavigate()` from React Router.

- Add the cancel button click handler `onClick()` with `useNavigate()` in `src/routes/edit.jsx`

Now when the user clicks "Cancel", they'll be sent back one entry in the browser's history.

> 🧐 Why is there no `event.preventDefault` on the button?

A `<button type="button">`, while seemingly redundant, is the HTML way of preventing a button from submitting its form.

Two more features to go. We're on the home stretch!

## URL Search Params and GET Submissions

https://reactrouter.com/en/main/start/tutorial#url-search-params-and-get-submissions

All of our interactive UI so far have been either links that change the URL or forms that post data to actions. The search field is interesting because it's a mix of both: it's a form but it only changes the URL, it doesn't change data.

Right now it's just a normal HTML <form>, not a React Router <Form>. Let's see what the browser does with it by default.

- Type a name (e.g. _Albert Einstein_) into the search field and hit the enter key

Note the browser's URL now contains your query in the URL as URLSearchParams:
`http://127.0.0.1:5173/?q=Albert+Einstein`

If we review the search form at `<div id="sidebar">` in `src/routes/root.jsx`, it looks like this:

```jsx
<form id="search-form" role="search">
  <input
    id="q"
    aria-label="Search contacts"
    placeholder="Search"
    type="search"
    name="q"
  />
  <div id="search-spinner" aria-hidden hidden={true} />
  <div className="sr-only" aria-live="polite"></div>
</form>
```

As we've seen before, browsers can serialize forms by the name attribute of it's input elements. The name of this input is `q`, that's why the URL has `?q=`. If we named it `search` the URL would be `?search=`.

> [!NOTE]  
> Note that this form is different from the others we've used, it does not have `<form method="post">`.
> The default method is `"get"`.
> That means when the browser creates the request for the next document,
> it doesn't put the form data into the request POST body,
> but into the `URLSearchParams` of a GET request.

## GET Submissions with Client Side Routing

https://reactrouter.com/en/main/start/tutorial#get-submissions-with-client-side-routing

Let's use client side routing to submit this form and filter the list in our existing loader.

- Change `<form>` to `<Form>` at `<div id="sidebar">` in `src/routes/root.jsx`
- Filter the list if there are URLSearchParams in action `async function loader({ request })` in `src/routes/root.jsx`

Because this is a GET, not a POST, React Router does not call the `action`. Submitting a GET form is the same as clicking a link: only the URL changes. That's why the code we added for filtering is in the `loader`, not the `action` of this route.

This also means it's a normal page navigation. You can click the back button to get back to where you were.

## Synchronizing URLs to Form State

https://reactrouter.com/en/main/start/tutorial#synchronizing-urls-to-form-state

There are a couple of UX issues here that we can take care of quickly.

1.  If you click back after a search, the form field still has the value you entered even though the list is no longer filtered.
2.  If you refresh the page after searching, the form field no longer has the value in it, even though the list is filtered

In other words, the URL and our form state are out of sync.

- Return q from your loader and set it as the search field default value in `src/routes/root.jsx`

That solves problem (2). If you refresh the page now, the input field will show the query.

Now for problem (1), clicking the back button and updating the input. We can bring in `useEffect()` from React to manipulate the form's state in the DOM directly.

- Synchronize input value with the URL Search Params in `src/routes/root.jsx`

> 🤔 Shouldn't you use a controlled component and React State for this?

You could certainly do this as a controlled component, but you'll end up with more complexity for the same behavior. You don't control the URL, the user does with the back/forward buttons. There would be more synchronization points with a controlled component.

If you're still concerned, see the following.

Notice how controlling the input requires three points of synchronization now instead of just one. The behavior is identical but the code is more complex.

```jsx
import { useEffect, useState } from "react";
// existing code

export async function loader({ request }) {
  const url = new URL(request.url);
  const q = url.searchParams.get("q") || "";
  const contacts = await getContacts(q);
  return { contacts, q };
}

// existing code

export default function Root() {
  const { contacts, q } = useLoaderData();
  const [query, setQuery] = useState(q);
  const navigation = useNavigation();

  useEffect(() => {
    setQuery(q);
  }, [q]);

  return (
    <>
      <div id="sidebar">
        <h1>React Router Contacts</h1>
        <div>
          <Form id="search-form" role="search">
            <input
              id="q"
              aria-label="Search contacts"
              placeholder="Search"
              type="search"
              name="q"
              value={query}
              onChange={(e) => {
                setQuery(e.target.value);
              }}
            />
            {/* existing code */}
          </Form>
          {/* existing code */}
        </div>
        {/* existing code */}
      </div>
    </>
  );
}
```

Complexity added:

```jsx
import { useEffect, useState } from "react";
```

```jsx
const q = url.searchParams.get("q") || "";
```

```jsx
const [query, setQuery] = useState(q);
```

```jsx
useEffect(() => {
  setQuery(q);
}, [q]);
```

```jsx
value={query}
onChange={(e) => {
  setQuery(e.target.value);
}}
```

## Submitting Forms `onChange`

We've got a product decision to make here. For this UI, we'd probably rather have the filtering happen on every key stroke instead of when the form is explicitly submitted.

We've seen `useNavigate()` already (`src/routes/edit.jsx`), we'll use its cousin, `useSubmit()`, for this.

- Add `useSubmit()` in `src/routes/root.jsx`

Now as you type, the form is submitted automatically!

Note the argument to _`submit()`_. We're passing in `event.currentTarget.form`. The `currentTarget` is the DOM node the event is attached to, and the `currentTarget.form` is the input's parent form node. The `submit()` function will serialize and submit any form you pass to it.

## Adding Search Spinner

https://reactrouter.com/en/main/start/tutorial#adding-search-spinner

In a production app, it's likely this search will be looking for records in a database that is too large to send all at once and filter client side. That's why this demo has some faked network latency.

Without any loading indicator, the search feels kinda sluggish. Even if we could make our database faster, we'll always have the user's network latency in the way and out of our control. For a better UX, let's add some immediate UI feedback for the search. For this we'll use _`useNavigation()`_ again.

- Add the search spinner in `src/routes/root.jsx`

The `navigation.location` will show up when the app is navigating to a new URL and loading the data for it. It then goes away when there is no pending navigation anymore.

## Managing the History Stack

https://reactrouter.com/en/main/start/tutorial#managing-the-history-stack

Now that the form is submitted for every key stroke, if we type the characters "seba" and then delete them with backspace, we end up with 7 new entries in the stack 😂. We definitely don't want this.

We can avoid this by replacing the current entry in the history stack with the next page, instead of pushing into it.

- Use `replace` in `submit()` in `src/routes/root.jsx`

We only want to replace search results, not the page before we started searching, so we do a quick check if this is the first search or not and then decide to replace.

Each key stroke no longer creates new entries, so the user can click back out of the search results without having to click it 7 times 😅.

## Mutations Without Navigation

https://reactrouter.com/en/main/start/tutorial#mutations-without-navigation

So far all of our mutations (the times we change data) have used forms that navigate, creating new entries in the history stack. While these user flows are common, it's equally as common to want to change data _without_ causing a navigation.

For these cases, we have the _`useFetcher()`_ hook. It allows us to communicate with loaders and actions without causing a navigation.

The ★ button on the contact page makes sense for this. We aren't creating or deleting a new record, we don't want to change pages, we simply want to change the data on the page we're looking at.

- Change the <Favorite> form to a fetcher form in `src/routes/contact.jsx`

Might want to take a look at that form while we're here. As always, our form has fields with a `name` prop. This form will send _`formData`_ with a favorite key that's either `"true" | "false"`. Since it's got `method="post"` it will call the action. Since there is no `<fetcher.Form action="...">` prop, it will post to the route where the form is rendered.

- Create the action `async function action({ request, params })` in `src/routes/contact.jsx`

Pretty simple. Pull the form data off the request and send it to the data model.

- Configure the route's new action in `src/main.jsx`

Alright, we're ready to click the star next to the user's name!

Check that out, both stars automatically update. Our new `<fetcher.Form method="post">` works almost exactly like the `<Form>` we've been using: it calls the action and then all data is revalidated automatically - even your errors will be caught the same way.

There is one key difference though, it's not a navigation - the URL doesn't change, the history stack is unaffected.

## Optimistic UI

https://reactrouter.com/en/main/start/tutorial#optimistic-ui

You probably noticed the app felt kind of unresponsive when we clicked the favorite button from the last section. Once again, we added some network latency because you're going to have it in the real world!

To give the user some feedback, we could put the star into a loading state with _`fetcher.state`_ (a lot like `navigation.state` from before), but we can do something even better this time. We can use a strategy called "optimistic UI"

The fetcher knows the form data being submitted to the action, so it's available to you on `fetcher.formData`. We'll use that to immediately update the star's state, even though the network hasn't finished. If the update eventually fails, the UI will revert to the real data.

- Read the optimistic value from `fetcher.formData` in `src/routes/contact.jsx`

If you click the button now you should see the star immediately change to the new state. Instead of always rendering the actual data, we check if the fetcher has any `formData` being submitted, if so, we'll use that instead. When the action is done, the `fetcher.formData` will no longer exist and we're back to using the actual data. So even if you write bugs in your optimistic UI code, it'll eventually go back to the correct state.

## Not Found Data

https://reactrouter.com/en/main/start/tutorial#not-found-data

What happens if the contact we're trying to load doesn't exist?

Our root _`errorElement`_ is catching this unexpected error as we try to render a `null` contact. Nice the error was properly handled, but we can do better!

Whenever you have an expected error case in a loader or action–like the data not existing–you can `throw`. The call stack will break, React Router will catch it, and the error path is rendered instead. We won't even try to render a `null` contact.

- Throw a 404 response in the loader in `src/routes/contact.jsx`

Instead of hitting a render error with `Cannot read properties of null`, we avoid the component completely and render the error path instead, telling the user something more specific.

This keeps your happy paths, happy. Your route elements don't need to concern themselves with error and loading states.

## Pathless Routes

https://reactrouter.com/en/main/start/tutorial#pathless-routes

One last thing. The last error page we saw would be better if it rendered inside the root outlet, instead of the whole page. In fact, every error in all of our child routes would be better in the outlet, then the user has more options than hitting refresh.

We could add the error element to every one of the child routes but, since it's all the same error page, this isn't recommended.

There's a cleaner way. Routes can be used without a path, which lets them participate in the UI layout without requiring new path segments in the URL.

- Wrap the child routes in a pathless route in `src/main.jsx`

When any errors are thrown in the child routes, our new pathless route will catch it and render, preserving the root route's UI!

## JSX Routes

https://reactrouter.com/en/main/start/tutorial#jsx-routes

And for our final trick, many folks prefer to configure their routes with JSX. You can do that with `createRoutesFromElements()`. There is no functional difference between JSX or objects when configuring your routes, it's simply a stylistic preference.

- Rewrite routes in `src/main.jsx`
