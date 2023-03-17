---
created: 2023-03-16 14:04
updated: 2023-03-16 14:16
---
---
**Links**: [[115 React Index]]

---
## Conditional Rendering
- We can do conditional rendering using `&&`

```jsx
export default function Joke(props) {
  const [isShown, setIsShown] = React.useState(false);
  function toggleShown() {
    setIsShown((prevShown) => !prevShown);
  }
  return (
    <div>
      {isShown && <p>{props.punchline}</p>}
      <button onClick={toggleShown}>
	      {isShown ? "Hide" : "Show"} Punchline
	  </button>
    </div>
  );
}
```

> [!note]- `&&` is generally **used when we want to display or NOT display something based on some condition**. 
> - Ternary operator is used when we want to display certain text based on some condition.
> - If the condition is very complex or has a lot of statements, we should consider using if statements or switch case instead of a ternary operator.