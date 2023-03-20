---
created: 2023-03-19 10:30
updated: 2023-03-19 18:45
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