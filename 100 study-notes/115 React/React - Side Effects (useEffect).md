---
created: 2023-03-16 14:54
updated: 2023-03-16 16:38
---
---
**Links**: [[115 React Index]]

---
## Side Effects
- In general we will always have to make API calls to fetch some data in React.
- Now the problem is that if we fetch the data using the following logic out component will keep on rendering infinitely.

```jsx
import React from "react";

export default function App() {
  const [starWarsData, setStarWarsData] = React.useState({});

  console.log("Component rendered");

  fetch("https://swapi.dev/api/people/1")
    .then((res) => res.json())
    .then((data) => setStarWarsData(data));

  return (
    <div>
      <pre>{JSON.stringify(starWarsData, null, 2)}</pre>
    </div>
  );
}
```

- The reason for infinite re-renders is that every time the component is initialised there will be an API call. Now because of the API call `setStarWarsData` will be called which will re-render the component which will make the API call again and this will keep on continuing.
- Here API call is a **side effect**.
	- Side effect are things which are outside the control of React. 
	-  Some other examples are: *local storage*, *web sockets*, keeping two states in sync.
- We use the `useEffect` to manage these side effects.
	- It helps *synchronise react state with outside systems*.

## `useEffect`
- The function we pass to `useEffect` **will run after the render of our component**.

```jsx
import React from "react";

export default function App() {
  const [count, setCount] = React.useState(0)
  console.log("Component rendered");

  React.useEffect(() => {
    console.log("Effect ran");
  });

  return (
    <div>
      <button onClick={() => setCount(prevCount => prevCount + 1)}>
	      Add
	  </button>
    </div>
  );
}

// Component rendered
// Effect ran
```

- Now if we click the Add button the component will be re-rendered again and we will see the same 2 console log outputs again.
- The second parameter to `useEffect` is known as the **dependencies array**.
	- *Dependencies array contains values which when changed will cause the `useEffect` to run*.

> [!note]+ Dependencies array *determines when the `useEffect` function will run instead of running after every single render*.
> - If we leave it as an empty array (`[]`) it tells React that there are no dependencies to watch out for. 
> - This means it **runs only once** when the component first loads and that's it.
> - If we wanted `useEffect` to run every time count changed we would have count in the dependencies array (`[count]`).

- Having a constant value in the dependencies array is same as having an empty array.
- `useEffect` won't be run if the values of the dependencies in the dependency array remain the same.
	- If count is in the dependencies array then `useEffect` will only be run if the value of count changes.

- Creating infinite renders using `useEffect`

```jsx
import React from "react";

export default function App() {
  const [count, setCount] = React.useState(0)
  console.log("Component rendered");

  React.useEffect(() => {
    console.log("Effect ran");
	setCount(count++)
  }, [count]);

  return (
    <div>
      <button onClick={() => setCount(prevCount => prevCount + 1)}>
	      Add
	  </button>
    </div>
  );
}
```

- This will cause infinite re-renders since we are depending on count and we are setting count inside the `useEffect`

> [!caution]- We should never modify the state in the dependencies array inside the `useEffect` body otherwise it will cause an infinite re-renders.

- In this example we want our `useEffect` to run every time our count changes by clicking the next character button.

```jsx
export default function App() {
  const [starWarsData, setStarWarsData] = React.useState({});
  const [count, setCount] = React.useState(1);

  React.useEffect(
    function () {
      console.log("Effect ran");
      fetch(`https://swapi.dev/api/people/${count}`)
        .then((res) => res.json())
        .then((data) => setStarWarsData(data));
    },
    [count] // important, this will run useEffect everytime count is changed i.e. the button is clicked
  );

  return (
    <div>
      <h2>The count is {count}</h2>
      <button onClick={() => setCount((prevCount) => prevCount + 1)}>
        Get Next Character
      </button>
      <pre>{JSON.stringify(starWarsData, null, 2)}</pre>
    </div>
  );
}
```

- Example to track the window width live:
	- Since window width is something outside of React we have to use `useEffect`

```jsx
import React from "react";

export default function WindowTracker() {
  const [windowWidth, setWindowWidth] = React.useState(window.innerWidth);

  React.useEffect(() => {
    window.addEventListener("resize", function () {
      setWindowWidth(window.innerWidth);
    });
  }, [windowWidth]);

  return <h1>Window width: {windowWidth}</h1>;
}
```

### Clean up
- Clean up functions in `useEffect` helps us in **preventing memory leaks**.
- An example would be creating a websocket connection with a chat API using `useEffect`.
	- Now after creating the subscription when we try to unmount the component it is always a good idea to sever the websocket connection.

```jsx
import React from "react";

export default function WindowTracker() {
  const [windowWidth, setWindowWidth] = React.useState(window.innerWidth);

  React.useEffect(() => {
    function watchWidth() {
      console.log("Setting up...");
      setWindowWidth(window.innerWidth);
    }

    window.addEventListener("resize", watchWidth);

    // removing the event listener we have added
    return function () {
      console.log("Cleaning up...");
      window.removeEventListener("resize", watchWidth);
    };
  }, []);

  return <h1>Window width: {windowWidth}</h1>;
}
```

- An example of conditionally mounting and unmounting the component would be `{show && <WindowTracer />}`

### Using async functions
- If we want to use `async` way of making API calls in `useEffect` then we will have to *create another function*.
- The reason being we *CANNOT declare the call back function in `useEffect` to be `async`* because when we do that **a promise is returned** but React is *expecting a clean up function* and not a promise.

> [!note] `useEffect` takes a function as its parameter. If that function returns something, it needs to be a cleanup function. Otherwise, it should return nothing. If we make it an async function, it automatically returns a promise instead of a function. Therefore, if you want to use async operations inside of `useEffect`, you need to define the function separately inside of the callback function.

```jsx
// WRONG
React.useEffect(async () => {
	// API call using await
}

// CORRECT
React.useEffect(() => {
  // separate async function
  async function getMemes() {
    const res = await fetch("https://api.imgflip.com/get_memes");
    const data = await res.json();
    setAllMemes(data.data.memes);
  }
  getMemes();
}, []);
```