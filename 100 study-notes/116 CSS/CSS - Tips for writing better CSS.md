---
created: 2023-03-19 18:47
updated: 2023-03-20 11:05
---
---
**Links**: [[116 CSS Index]]

---
> [!note] Try to analyze the design before you start writing CSS.

## Tips for writing better CSS
- Always try to separate your layout and content styling into different declarations

```css
/* NOT Advised since it is doing 2 jobs. Setting the typography as well as creating the layout */
.columns {
	font-size: 2rem;
	color: purple;
	display: grid;
	gap: 1em;
	grid-template-columns: 1fr 1fr
}

/* Better way of doing it */
.columns {
	display: grid;
	gap: 1em;
	grid-template-columns: 1fr 1fr
}

.content-style {
	font-size: 2rem;
	color: purple;
}
```

- Try to use classes for everything.
- When choosing colours for text and background always consider the *contrast ratio*.
	- ![[attachments/Pasted image 20230319191806.png]]
- Units to choose:
	- *Font size*: `rem`
	- *Width*: `%` coupled with `max-width`
	- *Height*: `%` coupled with `min-height`
	- *Padding, Margin*: `em` or `rem`
		- Using `em` for padding on buttons. 
			- So **if we change the font size of the button then padding will grow along with the font size**.
	- *Media queries*: `em`
- Don't use pixels. 
	- `em`, `rem` and `%` should be more than enough.
- If you want to use inline elements but want them to occupy the full width of the parent, nest them in a div block.
	- An example would be using anchor tag as a button but I want it to occupy the full width of the parent element. Now since anchor tag is an inline element, I need to wrap it in a div block.

 