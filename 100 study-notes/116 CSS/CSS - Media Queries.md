---
created: 2023-03-19 20:28
updated: 2023-03-19 22:09
---
---
**Links**: [[116 CSS Index]]

---
## Media Queries
- The following tag is important in the head of html to allow media queries: 
	- `<meta name="viewport" content="width=device-width, initial-scale-1.0>`
```css
@media (max-width: 500px) {
	body {
		color: blue;
	}
}
```
- The above media query means that if the width is **less than 500px** then apply this style on body.
- Use `min-width` if you *planning to build the website mobile first*.
	- It is easier to start from the smallest and then work our way to the largest.
- It is important to keep cascading in mind when writing media queries
```css
@media (max-width: 500px) {
	body {
		color: blue;
	}
}

body {
	color: red;
}
```
- With the above CSS the body will always be of colour red irrespective of the screen width because both the statements have the same specificity but the *second one is at the bottom* so cascading takes place.

> [!note] It is a good idea to have all the media queries at the bottom.