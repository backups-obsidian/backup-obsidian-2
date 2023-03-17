---
created: 2023-03-17 16:23
updated: 2023-03-17 16:35
---
---
**Links**: [[116 CSS Index]]

---
## Box Model
- HTML elements consist of *border, padding, margins and content*.
	- All these affect the space an element takes up.
- **Padding and border is inside of the element** so adding it increases the size of the element.
- **Margin is outside of the element** and it used to create spacing between sibling elements.

> [!note]- If we set the `box-sizing: border-box` then **everything (padding, border) will add upto the specified width and height**.
> So if the component has a height and width of 100px then adding a padding of 10px will make its height 120px and width 120px without `box-sizing: border-box`.
