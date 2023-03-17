---
created: 2023-03-16 14:25
updated: 2023-03-16 14:52
---
---
**Links**: [[115 React Index]]

---
## Forms
- In case of forms in React we need to record every change to form and record it to the state.

```jsx
import React from "react";

export default function Form() {
  const [firstName, setFirstName] = React.useState("");
  function handleChange(event) {
    setFirstName(event.target.value);
  }

  return (
    <form>
      <input type="text" placeholder="First Name" onChange={handleChange} />
    </form>
  );
}
```

- Using a single state value for for multiple form elements
	- We create an state object with different form values and use the `name` property to distinguish the events.

```jsx
export default function Form() {
  const [formData, setFormData] = React.useState({
    firstName: "",
    lastName: "",
  });

  function handleChange(event) {
    setFormData((prevFormData) => {
      return {
        ...prevFormData,
        [event.target.name]: event.target.value,
      };
    });
  }

  return (
    <form>
      <input
        type="text"
        placeholder="First Name"
        onChange={handleChange}
        name="firstName" // important having a single object
      />
      <input
        type="text"
        placeholder="Last Name"
        onChange={handleChange}
        name="lastName"
      />
    </form>
  );
}
```

### Controlled Inputs
- Currently we have 2 different state:
	- The one maintained by React
	- The one we are typing in the form

- To make controlled inputs we just **pass the React state value to the form value**.
	- Now **state is telling what the input box** should be *instead of input box telling what the state should be*.

```jsx
import React from "react";

export default function Form() {
  const [formData, setFormData] = React.useState({
    firstName: "",
    lastName: "",
  });

  function handleChange(event) {
    setFormData((prevFormData) => {
      return {
        ...prevFormData,
        [event.target.name]: event.target.value,
      };
    });
  }

  return (
    <form>
      <input
        type="text"
        placeholder="First Name"
        onChange={handleChange}
        name="firstName"
        value={formData.firstName} // controlled input change
      />
      <input
        type="text"
        placeholder="Last Name"
        onChange={handleChange}
        name="lastName"
        value={formData.lastName}
      />
    </form>
  );
}
```

> [!note]- With this we can do ensure things like the field values are lower case even if someone types capital letters.
> `event.target.value.toLowerCase()`

### Form submission
- By default when a form is submitted the page is refreshed. 
	- To prevent the default behaviour we can use `event.preventDefault()`

```jsx
function handleSubmit(event) {
    event.preventDefault()
    submitToApi(formData)
}

return (
    <form onSubmit={handleSubmit}>
        <input
            type="text"
            placeholder="First Name"
            onChange={handleChange}
            name="firstName"
            value={formData.firstName}
        />
    </form>
)
```

> [!note] If we have a *button element in inside the form element* then it *automatically acts as a submit button* for that form.