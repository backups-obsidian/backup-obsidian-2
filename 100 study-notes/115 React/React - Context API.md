---
created: 2023-03-26 20:56
updated: 2023-03-26 21:34
---
---
**Links**: [[115 React Index]]

---
## Problems with prop drilling
- One problem of prop drilling is that it results in **unnecessary re-rendering of components if the state (being passed as prop) changes**.
	- For example lets assume that there is a state which is being used by parent and grandchild.
	- But this state was prop drilled through child even though it doesn't need it.
	- Now whenever the state changes, the prop changes and all the components using that prop will be re-rendered causing unnecessary re-renders of the child component.
- We are passing props to some components which are not needed.

## Context API
- Context API is a **state management solution** provided by React.
- Context API aims to *solve prop drilling*.
- We first create a context using `createContext`.
- After creating a context we create a *context provider*.
- Context provider is a component which we wrap around all the components to which we want to provide state information.
	- All the components wrapped with the context provider and their children will have access to the context.
- We can have multiple context in our application.
- Simple example
```jsx
import { createContext, useState, useContext } from "react";

// We will be using this in components wrapped by the provider to get access to the context. Hence we need to export it.
export const AppContext = createContext(null);

const Parent = () => {
  const [userName, setUserName] = useState(0);
  return (
    <AppContext.Provider state={{ userName, setUserName }}>
      <Child />
    </AppContext.Provider>
  );
};

const Child = () => {
  return <GrandChild />;
};

const GrandChild = () => {
  const { userName } = useContext(AppContext);
  return <h1>{userName}</h1>;
};

export default Parent;
```

> [!note]- When a single value of the context changes then the whole context changes which means **all the components will be re-rendered** in that context.
> ![[attachments/Pasted image 20230326212147.png]]
> In the above example if either userName, setUserName, count or setCount changes then all the Child components will be re-rendered irrespective of whether they are using the context value that was changed.

- If we *have states that will be constantly changing*, we **should NOT put them inside a context**.

### Complex Example
- It is *advised to create a component for each context provider we create*.
```jsx
// UserContextProvider
import React, { createContext, useState } from "react";

// We will be using this in components wrapped by the provider to get access to the context. Hence we need to export it.
export const UserContext = createContext(null);

export const UserContextProvider = ({ children }) => {
  const [userInfo, setUserInfo] = useState(null);
  const [isAuth, setIsAuth] = useState(false);

  const login = () => {
    fetch("/login").then((res) => {
      setIsAuth(true);
      setUserInfo(res.user);
    });
  };

  const logout = () => {
    fetch("/logout").then((res) => {
      setIsAuth(false);
      setUserInfo(null);
    });
  };

  const value = {
    userInfo,
    setUserInfo,
    isAuth,
    setIsAuth,
    login,
    logout,
  };

  return (
    <UserContext.Provider value={value}> {children} </UserContext.Provider>
  );
};

export default UserContext;

// Main App component

const App = () => {
	return (
		<UserContextProvider>
			<OtherComponents />
		</UserContextProvider>
	)
}
```

- In the above example we are decoupling code and logic.