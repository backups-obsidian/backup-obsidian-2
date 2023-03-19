---
created: 2023-03-19 10:30
updated: 2023-03-19 15:42
---
---
**Links**: [[116 CSS Index]]

---
## Different ways of hiding an element
- `display: none;`: 
	- **Removes it from the document**.
	- It affects the placement of other elements.
- `visibility: hidden;`: 
	- Simply makes it **invisible**.
	- It *DOES NOT affect the placement of other elements*.

## Pseudo Classes
- We can use pseudo classes to change the behaviour of the element on click, hover etc.

- Example of pseudo classes with pseudo elements
	- On hover add an arrow before the content of the anchor tag.
```css
ul a:hover::before {
	content: "⇨ ";
	color: red;
	margin-right: 20px;
}
```
 
## Miscellaneous
- When you set a *width for the parent*, the *children DONT escape* that width.
- In general when we change the width the height of the element also changes to accommodate for the content.
	- Think of it as total area of the content remains the same so if you decrease the width then the height will automatically increase.
- We *DONT want to set height of parent elements*, *we want to let the children dictate the height*.
	- Manually declaring height of parent elements can lead to overflow issues.
 


## Useful CSS snippets
- To centre some content which is not of full width. Like some blogs with equal empty spaces on the left and right hand side.
	- ![[attachments/Pasted image 20230319154258.png]]
```css
.center {
	width: 70%;
	margin: 0 auto;
}
```

- CSS Reset:
```css
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

html {
  font-size: 16px;
}

body {
  min-height: 100vh; /* its fine even if we don't have it */
  width: 100%; /* 100wh will result in a bottom scroll bar */
}
```

## Questions

## References
