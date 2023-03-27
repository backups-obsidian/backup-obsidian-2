---
created: 2023-03-15 16:25
updated: 2023-03-26 17:44
---
---
**Links**: [[115 React Index]]

---
## Arrays
- **If we provide react an array it knows how to map over it and turn it into elements**.

```jsx
export default function App() {
    const colors = [
            <h3>Red</h3>, 
            <h3>Orange</h3>, 
        ]
    return (
        <div>
            {colors}
        </div>
    )
}
```

> [!note] We are providing react with an array inside `{}`. Also we are wrapping it in a `div` since we can return only 1 parent.

- Another Example: 
	- Here jokesData is JSON data.
	- jokeElements is an array that we are returning for react to map over it.

```jsx
export default function App() {
    const jokeElements = jokesData.map(joke => {
        return <Joke setup={joke.setup} punchline={joke.punchline} />
    })
    return (
        <div>
            {jokeElements}
        </div>
    )
}
```

> [!important] In the above example we use `{}` to pass props to `Joke` since we have to get a particular value from the `joke` object.

> [!question]+ What do we usually use `.map()` for in React?
> In web applications it is really common for us to have an array of objects. In react we take array of objects map over it and **convert it to an array of JSX element** that can be displayed on the page.

- Another example:
```jsx
function App() {
    const thingsArray = ["Thing 1", "Thing 2"]
    const thingsElements = thingsArray.map(thing => <p key={thing}>{thing}</p>)
    return (
        <div>
            {thingsElements}
        </div>
    )
}
```

- Each list item in React needs a key.
	- This helps React identify which items have changed, added or removed.
	- This is necessary since *React reacts to the change in state and re-renders the JSX*.

> [!note] The paragraph elements (`<p>`) are not in quotes i.e. they are not string and thing is in `{}` since it has to be interpreted later.

> [!caution]- We CANNOT use this `key` inside our props.
> If our child component needs some unique id we would need to pass it. If we try to do `props.key` it would be undefined.