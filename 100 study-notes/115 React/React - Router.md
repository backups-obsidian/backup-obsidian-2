---
created: 2023-03-26 18:02
updated: 2023-03-26 19:44
---
---
**Links**: [[115 React Index]]

---
## React Router
- We can install react router using `npm i react-router-dom`.
- React router **routes components**.
- Simple example
```jsx
import { BrowserRouter } from "react-router-dom";

ReactDOM.render(
  <React.StrictMode>
    <BrowserRouter>
      <App />
    </BrowserRouter>
  </React.StrictMode>,
  document.getElementById("root")
)
```

- App component
```jsx
import { Routes, Route } from "react-router-dom";

const App = () => {
  return (
	<NavBar /> // this means we will always display the navbar no matter the route
    <Routes>
      <Route path="/about" element={<About />}/>
      <Route path="/" element={<Home />} />
      <Route path="*" element={<NoRoute />} /> // match all other routes
    </Routes>
  );
};
```

> [!question]- What is the difference between `<Route>` and `<Routes>`
> - The `Route` component is used to render a specific component when the URL matches a certain path. When using multiple `Route` components, **React Router will render all `Route` components** that match the current URL.
> - The `Routes` component, on the other hand, is used to **render only the first `Route` that matches the current URL**. This means that if you have multiple `Route` components inside a `Routes`, only the first one that matches the current URL will be rendered.

### Navigate to internal routes
#### Link
- We use the `Link` component to navigate to internal routes.
```jsx
import { Link } from "react-router-dom";

export const Navbar = () => {
  return (
    <nav>
      <Link to="/">Home</Link>
      <Link to="/about">About</Link>
    </nav>
  );
};
```

- **If we want to navigate to an external link we can just use the anchor tag**.

#### NavLink
- If we want to style our links we would need to know which link we are on currently, this can be done using the `NavLink` component.
- When we are on a specific link an `active` class is added to it.
	- We can style the link by styling the `active` class.
- Another way of styling the link is using `styles`.
	- NavLink will automatically add `isActive` property to it as a prop.
```jsx
import { NavLink } from "react-router-dom";

export const Navbar = () => {
  const activeLink = (props) => {
    return {
      color: props.isActive ? "red" : "blue",
    };
  };
  return (
    <nav>
      <NavLink to="/" style={activeLink}>
        Home
      </NavLink>
      <NavLink to="/about" style={activeLink}>
        About
      </NavLink>
    </nav>
  );
};
```

> [!note]- `NavLink` should be used when we are building navigation bars or bread crumbs where it is important to know the active link. For all other use cases we should use `Link`

### Programatic Navigation
- Example: Navigate someone to some other page on form submission.
```jsx
import { useNavigate } from "react-router-dom";

const Home = () => {
  const navigate = useNavigate();

  const onClickHandler = () => {
    // some work
    navigate("/about");
  };
  return (
    <>
      <button onClick={onClickHandler}> Go to about </button>
      <h1> Home </h1>
    </>
  );
};

export default Home;
```

### Automatic Navigation
- Example: typing any bogus link redirects us to the home page.

```jsx
import { Navigate } from "react-router-dom";

export function NotFound() {
  return <Navigate to="/" />;
}
```

### Nested Routes
- React router automatically forms the full path to the children routes.
- Example:
	- `featured` is actually `/products/featured` similarly for `new`
		- ![[attachments/Pasted image 20230326191302.png]]
	- We also have to use the `Outlet` tag to render the nested components.
		- ![[attachments/Pasted image 20230326191359.png]]
- This is useful if you want to render the featured and the new component within the product component.
- If we don't want to do this we can do this without nesting by using `Route path='/products/featured'`.

### Dynamic Router
- Dynamic Routes: `<Route path= '/users/:userId' element={<UserDetails />} />`
	- `:userId` **can be string or number**
	- We use the `:userId` in the component using `useParams`
		- ![[attachments/Pasted image 20230326192102.png]]
		- Here `userId` is the same parameter we defined in the route.
- **React router will match the route that is more specific**.
	- If we have 2 different routes of `/users/:userId` and `/users/new` then going to `/users/new` will route to `/users/new`, any other value and it will be routed to `/users/:userId`.
	- The order of routes don't matter.

### State passing
- We can pass some state data while navigating from one link to another
	- `<Link to="/" state="Hi">`
- We get the state in the component using the `useLocation` hook from react router.
	- ![[attachments/Pasted image 20230326194311.png]]
- This state is not present in the URL bar.