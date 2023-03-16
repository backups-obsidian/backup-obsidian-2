---
created: 2023-03-15 16:47
updated: 2023-03-16 13:55
---
---
**Links**: [[115 React Index]]

---
## Event Listeners
```jsx
export default function App() {
    const handleClick = () => {
        console.log("I was clicked!")
    }
    
    return (
        <div className="container">
            <button onClick={handleClick}>Click me</button>
        </div>
    )
}
```

> [!note] We use `onClick` instead of `onclick` which is used in HTML tag for the same as [[React - Styling#^18d6dc| this]].

### How to pass values to event listener functions
- Imagine the event listener function is coming from the parent state but we want to pass it some value from the child component.

```jsx
// Box component
const Box = (props) => {
  return <div onClick={() => props.clickHandler(props.id)}> hello there</div>;
};

// Main App component
const App = () => {
  const clickHandler = (id) => {
    console.log(id); // 1
    // some state change logic
  };

  return <Box id="1" clickHandler={clickHandler} />;
};
```