---
created: 2023-03-14 16:33
updated: 2023-03-16 13:29
---
---
**Links**: [[115 React Index]]

---
## Styling
- Styling something in react is really simple we just need to add classes like we did in HTML.
- We use `className` instead of `class` (like in normal HTML) since react is converting our JS object to DOM elements. ^18d6dc
	- Whatever properties or attributes we add to the JSX are directly accessing the equivalent properties in JS.
	- For example when we create an element using `document.createElement` we style it using `className` hence we use `className` in JSX for styling.

```jsx
// creating a DOM element with styling
const h1 = document.createElement("h1")
h1.className = ""

// equivalent JSX
<h1 className="..."> Something </h1>
```

## Dynamic Styles
- Since we can use JS in React we can do **conditional styles**.
```jsx
const styles = {
  backgroundColor: props.darkMode ? "#222222" : "#cccccc",
};

return <div style={styles}> Dynamic Style </div>
```