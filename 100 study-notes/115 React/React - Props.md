---
created: 2023-03-14 18:10
updated: 2023-03-26 17:58
---
---
**Links**: [[115 React Index]]

---
## Props
- It is important to remember that **components are nothing but functions that return JSX**.
- Now since we want our functions to be reusable and dynamic we need to add parameters.
	- **Props are these properties/parameters that we pass to our components/functions**.
- When received by a component, *props are nothing but simple JS objects*.

### Passing props
```jsx
function App() {
    return (
        <div className="contacts">
            <Contact 
                img="./images/mr-whiskerson.png"
                name="Mr. Whiskerson"
                phone="(212) 555-1234"
                email="mr.whiskaz@catnap.meow"
            />
        </div>
    )
}
```

### Using Props
```jsx
export default function Contact(props) {
    return (
        <div className="contact-card">
            <img src={props.img}/>
            <h3>{props.name}</h3>
            <p>{props.phone}</p>
            <p>{props.email}</p>
        </div>
    )
}
```

> [!note]- The name of the keys inside props is same as what we passed.
> `console.log(props)`
> ![[attachments/Pasted image 20230315125012.png]]

> [!question]- Can I pass a custom prop (e.g. `blahblahblah={true}`) to a native
   DOM element? (e.g. `<div blahblahblah={true}`>)
> No, because the JSX we use to describe *native DOM elements will be turned into REAL DOM elements by React*. Real DOM elements only have the properties/attributes specified in the HTML specification which doesn't include our custom properties like `blahblahblah`.

### Prop Destructuring
```jsx
export default function Contact({img, name, phone, email}) {
    return (
        <div className="contact-card">
            <img src={img}/>
            <h3>{name}</h3>
            <p>{phone}</p>
            <p>{email}</p>
        </div>
    )
}
```
- **The name should be exactly the same as what was passed to the prop**.
- Example if we want to have different name for the props
```jsx
export default function Contact({img: image, name, phone, email}) {
    return (
        <div className="contact-card">
            <img src={image}/>
            <h3>{name}</h3>
            <p>{phone}</p>
            <p>{email}</p>
        </div>
    )
}
```

> [!tip]- In my opinion it is *best to just use the `props` object instead of destructuring* since it helps in distinguishing which attributes are being passed as props to the component (since we will have to use the ones passed as `props.attribute`) and which are defined within the component. 
> There is more typing but I feel it is worth it.

### Passing non string values to props
- It works on the idea that using `{}` we enter the JS land and then we can use any JS object type.
```jsx
<Joke 
    punchline="Some punchline"
    isPun={true}
    upvotes={10}
    comments={["hello", "there"]}
/>
```

### Default Props
- Default props are useful when we don't provide props to a component and don't want to receive an error.
	- The data for the props may be something we are expecting from an API

```jsx
const Header = ({ title }) => {
  return (
    <header>
      <h1>{title}</h1>
    </header>
  );
};

Header.defaultProps = {
  title: "Default Title",
};

export default Header;
```

### Passing data between components
- We *CANNOT directly pass data between children components*.
	- ![[attachments/Pasted image 20230316131214.png]]
- If we want to pass data between 2 child components we will have to *move the state to the parent component* and then *pass these states as props to the child components*.
	- ![[attachments/Pasted image 20230316131404.png]]
- I think the main reason react does this is because if we were to pass data between child components directly there is no way for React to know which parent components should be updated.
- Also there is *no way to pass data upwards*.
- We can manage global state using context.
- This way of passing data from the parent component to the child component is known as **prop drilling**.

> [!note]- Data flows in react from top to bottom. 
> - But try to limit the state to the components that need it. There is no need to initialise all the states at the top of the tree if the lower components don't need it.
> - **Keep state as local as you can** i.e. only to the components that need it.