---
created: 2023-03-14 15:10
updated: 2023-03-26 17:06
---
---
**Links**: [[115 React Index]]

---
## Introduction
- React is 
	- **Composable**: We can use components (small UI elements) 
		- React is a library that lets you organise UI code by *breaking it apart into pieces called components*.
	- **Declarative**: We just tell it what should be done and not how it should be done.
- Example *how React is declarative*.
```jsx
// vanilla js
const h1 = document.createElement("h1")
h1.textContent = "Hello world"
h1.className = "header"

// JSX
const element = <h1 className="header"> This is JSX </h1>
ReactDOM.render(element, document.getElementById("root"))
```

- **Difference between elements created by vanilla js and React**
```jsx
const h1 = document.createElement("h1")
h1.textContent = "Hello world"
h1.className = "header"
console.log(h1)

// <h1 class="header">

const element = <h1 className="header">This is JSX</h1>
console.log(element)

/*
{
    type: "h1", 
    key: null, 
    ref: null, 
    props: {
        className: "header", 
        children: "This is JSX"
    }, 
    _owner: null, 
    _store: {}
}
 */

// JSX
ReactDOM.render(element, document.getElementById("root"))
```

> [!note]+ With **JSX React is just creating a plain old JavaScript Object**. These objects *describe the DOM element* that we want React to put onto the page for us.
> - JSX is kind of like a function which when run returns us *JS objects that React can interpret `ReactDOM.render()` to create actual elements* that get put on the page.
> - The main work of `ReactDOM.render()` is to take react elements (JS objects) and convert them to DOM elements that the browser can understand.

> [!caution]- We can render **only 1 parent element**.

```jsx
// this is wrong
ReactDOM.render(<h1>Hello</h1><p>Wrong</p>, document.getElementById("root"))

// this is right (wrapped within a div)
ReactDOM.render(
  <div>
    <h1>Hello</h1>
    <p>Wrong</p>
  </div>,
  document.getElementById("root")
);

```

## Working with JSX
- Simple JSX
```jsx
const page = (
  <div>
    <h1 className="header">This is JSX</h1>
    <p>This is a paragraph</p>
  </div>
);

ReactDOM.render(page, document.getElementById("root"));
```

- If we try to `console.log(page)`, we see that it is much more complex than the previous one.
	- ![[attachments/Pasted image 20230314152259.png]]

> [!important]- We can *save JSX into a simple variable* because when we write JSX what **we are getting back is simple JavaScript object**.

### Using JS/Variables in JSX
- We do it using curly braces `{}`.
- We can think of it as JavaScript land when we see `{}`
	- **Anything we put inside `{}` will be run as JS code**.
 
```jsx
function App() {
    const date = new Date()
	const someVar = "Hello"
    return (
        <h1>{someVar} it is currently about {date.getHours() % 12}!</h1>
    )
}
```

- It is much *more readable to have JS outside of JSX* and just reference the variables in JSX. 
	- In this way we ensure *we are separating the logic from the display*.
		- ![[attachments/Pasted image 20230315092317.png]]

## Imports
- **For the react JSX syntax to work we have to import react**.
	- `import React from 'react'`
	- NOTE: From react version 17 we don't need to import React.
- For rendering the we need to import react-dom.
	- `import ReactDOM from 'react-dom'` - 17
	- `import ReactDOM from 'react-dom/client'`

## React 17 vs React 18

- Older way of doing it
```jsx
import React from "react";
import ReactDOM from "react-dom";

const navbar = (
  <nav>
    <h1>Bob's Bistro</h1>
  </nav>
);

ReactDOM.render(navbar, document.getElementById("root"));
```

- There is a **different way of rendering** in React version 18.
	- We first create the root element.

```jsx
import React from "react";
import ReactDOM from "react-dom/client";

const navbar = (
  <nav>
    <h1>Bob's Bistro</h1>
  </nav>
);

const root = ReactDOM.createRoot(document.getElementById("root"));
root.render(navbar);
```
