---
created: 2023-03-19 18:44
updated: 2023-03-19 20:18
---
---
**Links**: [[116 CSS Index]]

---
## Useful Snippets
- To centre some content which is not of full width. Like some blogs with equal empty spaces on the left and right hand side.
	- ![[attachments/Pasted image 20230319154258.png]]
```css
.center {
	width: 70%;
	margin: 0 auto;
}
```

- Centring something in a div.
```css
.some-div-class {
	display: grid;
	place-items: center;
}

/* If you want to centre something in a full page*/
body {
	min-height: 100vh;
	display: grid;
	place-items: center;
}
```

- **CSS Reset**:
```css
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font: inherit;
}

html {
  font-size: 16px;
}

body {
  min-height: 100vh; /* This is useful if you have something which won't occupy the full page but you want to center it horizontally and vertically  */
  width: 100%; /* 100wh will result in a bottom scroll bar */
}

/* Ensuring videos and images are responsive */
img, picture, svg, video {
  display: block;
  max-width: 100%;
}
```
