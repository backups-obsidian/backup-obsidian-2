---
created: 2023-03-14 16:33
updated: 2023-03-14 16:33
---
---
**Links**: [[115 React Index]]

---
## Styling
- Styling something in react is really simple we just need to add classes like we did in HTML.
- We use `className` instead of class since react is converting our JS object to DOM elements.
	- When we create an element using `document.createElement` we style it using `className`.

```jsx
// creating a DOM element with styling
const ul = document.createElement("ul")
ul.className = ""
```

