---
created: 2023-03-15 17:06
updated: 2023-03-16 13:20
---
---
**Links**: [[115 React Index]]

---
## State
- Without React if we make a change to our state (like changing the value of some array) the UI won't be updated automatically.
	- We will have to use JS to select the element (`document.getElementById`) and then making the changes.
- But as we know **React is declarative**, *we just need to update our data and React will automatically react to it to update our UI*.

### Different between props and state
- Props refers to the properties being passed into a component in order for it to work correctly, similar to how a function receives parameters. 
	- **Incoming props should be immutable and should never be changed manually** i.e. the props *should NEVER change from within the body of the component*.
		- You can think of it as fixed parameters that we are passing to a function which should never be changed inside the function body.
		- We can obviously change it by reassigning it something else but we may get a warning.
	- We can change the values that we pass to our component.

```jsx
// this is WRONG
function Navbar(props) {
	props.coverImage = "something else"
}

// this is OK
<Navbar image="hello" /> -> <Navbar image="hello2" />
```

- State refers to **values that are managed by the component**, similar to **variables declared inside a function**. 
	- Any time you have *changing values that should be saved/displayed, you'll likely be using state*.

### `useState`
- The first value that we give to `useState` is the default value of the state.
- We use the callback function returned to us at the time of state definition to change the state.
	- We cannot change the state directly like we do in normal JS since React needs to react to our state change and update the UI.

```jsx
export default function App() {
  const [isImportant, setIsImportant] = React.useState("Yes");
  function handleClick() {
	    setIsImportant("No");
    }
  }

  return (
    <div onClick={handleClick}>
      <h1>{isImportant}</h1>
    </div>
  );
}
```


> [!note]+ If you ever *need the old value of state* to help you determine the new value of state, **you should pass a callback function** to your state setter function instead of using state directly. 
> - This callback function will receive the old value of state as its parameter, which you can then use to determine your new value of state.
> - This is efficient and has to do with how react handles state changes and UI update internally.

```jsx
const [count, setCount] = React.useState(0);

// this is correct
function add() {
  setCount(prevCount => {
    return prevCount + 1;
  });
}

// this is not efficient
function subtract() {
	setCount(count - 1)
}
```

#### Using arrays
- When setting an array **we need to return a NEW array**.
- Using `prevArray.push()` won't return a new array and it would just modify the existing array. 
	- Moreover `.push()` operation returns an integer (number of items in the array).

```jsx
const [thingsArray, setThingsArray] = React.useState(["Thing 1", "Thing 2"]);

// wrong
function addItem() {
  setThingsArray((prevThingsArray) => {
    return prevThingsArray.push("Something") // returns an integer
  });
}

// right
function addItem() {
  setThingsArray((prevThingsArray) => {
    return [...prevThingsArray, "Something"]; // returning a new array
  });
}
```

#### Using Objects
```jsx
const [contact, setContact] = React.useState({
    firstName: "John",
    lastName: "Doe",
    phone: "+1 (719) 555-1212",
    email: "itsmyrealname@example.com",
    isFavorite: false
})

function toggleFavorite() {
    setContact(prevContact => {
        return {
            ...prevContact,
            isFavorite: true
        }
    })
}
```

- We are copying the whole `prevContact` and then overriding `isFavorite` attribute.

#### Combining state and props
- In this example a **child component is receiving state via props**.
```jsx
// App component
import React from "react";
import Count from "./Count";

export default function App() {
  const [count, setCount] = React.useState(0);

  function add() {
    setCount((prevCount) => prevCount + 1);
  }

  function subtract() {
    setCount((prevCount) => prevCount - 1);
  }

  console.log("App component rendered");

  return (
    <div className="counter">
      <button className="counter--minus" onClick={subtract}>
        –
      </button>
      <Count number={count} />
      <button className="counter--plus" onClick={add}>
        +
      </button>
    </div>
  );
}

// Count component
import React from "react";

export default function Count(props) {
  console.log("Count component rendered");

  return (
    <div className="counter--count">
      <h1>{props.number}</h1>
    </div>
  );
}
```

- When we first start our application we will see both the console log statements (App component rendered and Count Component rendered).
- Now if we click on the + or - button we will again see both the console log statements.

> [!note]- Whenever a **state changes React will re-render** the *component where state exists* (`App` in the above example) and *any child components that may rely on that state* to be working correctly (`Count` in the above example).

- If we want to provide the *ability to child components to change state* then we need to *pass them* **reference to setter functions**.

```jsx
// App Component
function toggleFavorite() {
  setContact((prevContact) => ({
    ...prevContact,
    isFavorite: !prevContact.isFavorite,
  }));
}

return <Star isFilled={contact.isFavorite} handleClick={toggleFavorite} />;

// Star Component
export default function Star(props) {
  const starIcon = props.isFilled ? "star-filled.png" : "star-empty.png";
  return (
    <img
      src={`../images/${starIcon}`}
      onClick={props.handleClick}
    />
  );
}
```

### Passing data between components
- We *CANNOT directly pass data between children components*.
	- ![[attachments/Pasted image 20230316131214.png]]
- If we want to pass data between 2 child components we will have to *move the state to the parent component* and then *pass these states as props to the child components*.
	- ![[attachments/Pasted image 20230316131404.png]]
- I think the main reason react does this is because if we were to pass data between child components directly there is no way for React to know which parent components should be updated.
- Also there is *no way to pass data upwards*.
- We can manage global state using context.

> [!note]- Data flows in react from top to bottom. 
> - But try to limit the state to the components that need it. There is no need to initialise all the states at the top of the tree if the lower components don't need it.
> - **Keep state as local as you can** i.e. only to the components that need it.