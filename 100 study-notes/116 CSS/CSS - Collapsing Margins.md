---
created: 2023-03-19 14:57
updated: 2023-03-19 15:32
---
---
**Links**: [[116 CSS Index]]

---
## Collapsing Margins
- Collapsing margins means that if the top element has a bottom margin of 20px and the bottom element has a top margin of 10px then the spacing between them will be 20px only.
	- ![[attachments/Pasted image 20230319151851.png]]

- Margin collapse *only happens on block level elements*.
- Collapsing margins will only occur in flow layout.
	- We can **void margin collapse by using flexbox, grid and position layouts**.
- Margin collapse *only apply to vertical margins*.
	- In case of horizontal margins they are added.
	- ![[attachments/Pasted image 20230319152438.png]]
- The biggest absolute value will win which means -60px will win over -40px.

> [!note]- Example: *margin collapse with nested elements*
> - In case of nested elements we think that adding a margin to the child will increase its distance from the parent element but instead the margin transfers to the parent element.
> - A better approach would be to just use padding to add space between the parent and child element.
> ---
> ![[attachments/Pasted image 20230319153108.png]]

