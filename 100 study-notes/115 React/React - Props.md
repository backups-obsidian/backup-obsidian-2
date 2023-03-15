---
created: 2023-03-14 18:10
updated: 2023-03-15 12:59
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