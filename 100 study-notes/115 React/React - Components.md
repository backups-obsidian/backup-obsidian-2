---
created: 2023-03-14 16:09
updated: 2023-03-14 17:58
---
---
**Links**: [[115 React Index]]

---
## Custom Components
- We *create custom components using functions*.

> [!note]- **A react component is a function that returns React elements**. React elements are the objects that get created when we return JSX.
> Sometimes components are also *referred to as UI*.

- We need to use **PascalCase as the name of functions**.
- To *call our components we wrap it in angle brackets* (`<ComponentName />`)

- Creating and using a simple component.
```jsx
const Page = () => {
    return (
        <ul> 
            <li> Job requirement </li>
            <li> Good to know </li>
        </ul> 
    )
}

ReactDOM.render(<Page />, document.getElementById("root"))
```

- Example on how to use use components together
```jsx
function Header() {
  return <header>Header</header>;
}

function Footer() {
  return <footer>Footer</footer>;
}

function Page() {
  return (
    <div>
      <Header />
      <Footer />
    </div>
  );
}

ReactDOM.render(<Page />, document.getElementById("root"));
```

> [!note]- Mind the `div` block in the above example since we cannot return 2 parent elements.

## Organising Components
- We can split the components into individual files and then import them.

```jsx
// App.js
function AppComponent() {
  return <h1>This is start</h1>;
}

export default App;

// index.js
import CanBeAnyName from "./App" // App here is the name of file
// It CanBeAnyName since we are doing a default export. 
// This isn't possible in named exports.

// Just keep the names same to avoid any confusion.
```