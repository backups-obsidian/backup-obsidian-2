---
created: 2023-03-19 15:35
updated: 2023-03-19 18:05
---
---
**Links**: [[116 CSS Index]]

---
## Responsive Layouts
- Avoid fixed sizes like `width: 600px`.
	- If you want to set fixed sizes then use properties like `max-width: 600px`. 
		- This means we cannot go greater than 600px.
	- Example
		- ![[attachments/Pasted image 20230319154459.png]]
		- ![[attachments/Pasted image 20230319154553.png]]

- Don't set heights.
	- If you want to set heights use properties like `min-height: 300px` to prevent overflow of content.
	- Example:
		- ![[attachments/Pasted image 20230319154808.png]]
		- ![[attachments/Pasted image 20230319154848.png]]
		- ![[attachments/Pasted image 20230319154922.png]]

- Try to write CSS keeping mobile first in mind.
	- *Generally it is easier to write the CSS for mobile because everything will mostly be a block element*, there isn't enough space to use grid or flexbox or do any other fancy stuff.
	- We can then use media queries to change the layout in case of large screen sizes.
 
> [!note]- In general we first write CSS for the desktop version of the website and then use media queries for the mobile version but it should be the other way round.
> When writing CSS for mobile first we will be using `min-width` media queries to write CSS for larger desktops.

- Try to have at max two different media queries breakpoint.
	- More than that and it would become very difficult to manage.

- Thinking about complex layouts
	- ![[attachments/Pasted image 20230319180401.png]]