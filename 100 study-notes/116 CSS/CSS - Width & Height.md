---
created: 2023-03-17 11:43
updated: 2023-03-17 12:35
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

> [!note]- Display behaviour of both the elements can be changed using CSS. 
> For example, *a block-level element can be made to display inline or vice versa*.

## Width & Height
- Use to change the width and height of the element. 
- **By default width and height can only be applied to elements that are NOT inline elements**.
	- We can change this behaviour by having `display: inline-block;` for inline elements.
 
> [!note]- Even after this adjusting the width and height of inline and block elements, *inline elements will start from the same line* and *block elements will start from a new line*.
> ![[attachments/Pasted image 20230317115433.png]]

### Display Units
- `px` represents a single pixel of display.
- Pixel units provide *easy and predictable results*.
- **With pixels unit it doesn't matter what the length of the view port** is (browser width), it will always be the same.
	- ![[attachments/Pasted image 20230317122151.png]]
- `em` are relative units.
- They are *relative to the default font size that the element has inherited*.
	- The default font-size is 16px so `1em = 16px`.
	- If we change the font-size of an element then that element and all the child elements will inherit that changed font-size for em calculations.
 
```css
/* progress is a child of bar */
/* Both bar and progress have a width of 160 px */
.bar {
  font-size: 8px;
  width: 20em;
}
.progress {
  font-size: 16px;
  width: 10em;
}
```

- `rem` is *relative only to the root element*.
	- It doesn't matter what font-size the element or the parent has.
 
```css
/* progress is a child of bar */
/* the font-size of bar and progress will have no effect on width. Both of them have the same width */
.bar {
  font-size: 8px;
  width: 20rem; 
}
.progress {
  font-size: 16px;
  width: 10rem;
}
```

- `%`: defines the width with respect to the parent element.
	- For example: `.bar { width: 50% }` means element with the class of bar is always 50% of the parent element.

> [!note] `em`, `rem` and `%` are useful for responsive web design.
