---
created: 2023-03-17 11:43
updated: 2023-03-20 11:15
---
---
**Links**: [[116 CSS Index]]

---
## Width & Height
- Use to change the width and height of the element. 
- **By default width and height can only be applied to elements that are NOT inline elements**.
	- We can change this behaviour by having `display: inline-block;` for inline elements.
 
> [!note]- Even after this adjusting the width and height of inline and block elements, *inline elements will start from the same line* and *block elements will start from a new line*.
> ![[attachments/Pasted image 20230317115433.png]]

> [!question]- What is meant by `width: auto`?
> - This is the default for most css elements.
> - This makes it expand to *occupy all available horizontal space within its containing block (parent element)*. 
> - If it has any horizontal padding or border, the widths of those do not add to the total width of the element.
> ---
> In short it means use whatever space is available without overflowing (horizontal scroll bar).

> [!caution]- We can set widths of elements but **DON'T set heights for elements** as they can lead to unexpected results if you don't know what you are doing.
> - If you have to set heights then always set `min-height`.
> - `min-height` will always grow to fit the content.

- When you set a *width for the parent*, the *children DONT escape* that width.
- In general when we change the width the height of the element also changes to accommodate for the content.
	- Think of it as total area of the content remains the same so if you decrease the width then the height will automatically increase.
- We *DONT want to set height of parent elements*, *we want to let the children dictate the height*.
	- Manually declaring height of parent elements can lead to overflow issues.

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

- `%`: defines the height/width **with respect to the parent element**.
	- For example: `.bar { width: 50% }` means element with the class of bar has a width which is always 50% of the parent element.

> [!note] `em`, `rem` and `%` are useful for responsive web design.

## Understanding browser height and width
- Setup
```html
<html>
  <head></head>
  <body></body>
</html>
```

```css
* {
  box-sizing: border-box;
  padding: 0;
  margin: 0;
}

body {
  border: 2px solid red;
}
```

- The body element inherits from html element which inherits from the **viewport (size of the browser window)**.
- If nothing is specified **only the view port width is inherited by the html element** and *NOT the view port height*. So the height of the page is always equal to sum of height of all elements.
	- ![[attachments/Pasted image 20230318190935.png]]
	- ![[attachments/Pasted image 20230318191026.png]]

```css
* {
  box-sizing: border-box;
  padding: 0;
  margin: 0;
}

body {
  border: 2px solid red;
  min-height: 100vh;
}
```

- But now if we change the css to the above we ensure that we are using the full page. 
- Since we have expanded the body the height of the html also changes.
	- ![[attachments/Pasted image 20230318191501.png]]
	- ![[attachments/Pasted image 20230318191554.png]]

> [!question]- Should I use `width: auto` or `width: 100%`.
> If you have a margin for an element and use `width: 100%` then this might result in a horizontal scroll bar if the width of the parent element is equal to view port width. 
> Since content will be displayed on the full view port and then we also have to account for the margin.
> Whereas auto tries to fit the content in the available space.
> ---
> [CSS width auto vs 100% | What's the difference? - YouTube](https://www.youtube.com/watch?v=-st14lUQD3U)

