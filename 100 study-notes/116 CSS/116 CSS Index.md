---
created: 2023-03-16 18:18
updated: 2023-03-18 09:38
---
---
**Links**: [[116 CSS Index]]

---
## Basics
[[CSS - Introduction]]
[[CSS - Selectors, Properties & Values ]]
[[CSS - Specificity]]
[[CSS - Width & Height]]
[[CSS - Colors]]
[[CSS - Box Model]]

- **Element flow**:
	- Inline element only take the amount of space needed for the content inside. 
	- **Inline elements will stack horizontally** floating to the left until they no longer fit. 
		- ![[attachments/Pasted image 20230317164821.png]]
		- ![[attachments/Pasted image 20230317164846.png]]
	- **Block level elements will always stack vertically** even if they can fit next to each other.

[[CSS - Position]]
[[CSS - Flexbox]]

- Different ways of hiding the element:
	- `display: none;`: 
		- Removes it from the document.
		- It affects the placement of other elements.
	- `visibility: hidden;`: 
		- Simply makes it invisible.
		- It *DOES NOT affect the placement of other elements*.


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
