---
created: 2023-03-16 18:18
updated: 2023-03-20 11:30
---
---
**Links**: [[116 CSS Index]]

---
## Inline vs Block elements in HTML
- Inline elements are those that are used *within text* and **DO NOT start on a new line**. 
- Examples include `<a>`, `<b>`, `<strong>`, `<em>`, `<span>`, `<img>`, and `<input>`. 
- **Inline elements only take up the space they need to display their content** and can be placed within other inline elements or block-level elements.
- Block-level elements, on the other hand, **start on a new line** and **take up the full width of their parent element by default**. 
- Examples of block-level elements include `<p>`, `<div>`, `<ul>`, `<ol>`, `<li>`, `<h1>` through `<h6>`, and `<table>`. 
- Block-level elements can contain both inline and other block-level elements.

## Display
- **Element flow**:
	- Inline element only take the amount of space needed for the content inside. 
	- **Inline elements will stack horizontally** floating to the left until they no longer fit. 
		- ![[attachments/Pasted image 20230317164821.png]]
		- ![[attachments/Pasted image 20230317164846.png]]
	- **Block level elements will always stack vertically** even if they can fit next to each other.
		- ![[attachments/Pasted image 20230320111028.png]]
- *Block level elements* have a width of 100% (**100% width of the parent**).
	- So the *only thing limiting the size of block level elements is their parent*.

> [!note]- We **CANNOT apply height, width, padding and margin to inline elements**.
> Padding and margin works for inline elements visually but it does some weird stuff.

- But sometimes we need to *apply padding and margin to inline elements* like anchor tags to make them look like buttons.
	- This is where we use `display: inline-block`
	- The reason we DONT use `display: block` for a tag when styling it as button is that it would occupy the full width of the parent block.
		 - ![[attachments/Pasted image 20230320112144.png]]
	- Just like inline elements if there is space *inline-block elements will stack horizontally*.
		- ![[attachments/Pasted image 20230320112355.png]]

> [!tip] So with inline-block elements we are getting inline elements for which we can set padding and margin.

- We can achieve thing with flex box by having a flexbox container over the anchor tag but I feel it is over complicating stuff.

## Different ways of hiding an element
- `display: none;`: 
	- **Removes it from the document**.
	- It affects the placement of other elements.
- `visibility: hidden;`: 
	- Simply makes it **invisible**.
	- It *DOES NOT affect the placement of other elements*.