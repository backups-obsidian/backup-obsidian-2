---
created: 2023-03-16 18:18
updated: 2023-03-21 14:04
---
---
**Links**: [[116 CSS Index]]

---
## Position
- All elements have a **default position of static** i.e. they *render in the order they appear in the document*.
- They follow the normal element flow.
- Once we have a *position anything other than static* we can use *top left right and bottom to position it further*.

> [!note]- Positioning of *absolute* and *fixed* remove the element from the element flow.

- For examples rough HTML document:

```html
<section>
	<div class="box first">
		<p> content </p>
	</div>
	
	<div class="box second">
		<div class="ribbon">️❤</div>
		<p> content </p>
	</div>
</section>

<footer> </footer>
```

### `position: relative`
- Original document for visual examples (first box):
	- ![[attachments/Pasted image 20230317170400.png]]
- With this we **position the element relative to the original position in the element flow**.
- Having a position of relative and top of -40px.
	- ![[attachments/Pasted image 20230317170537.png]]
- This *DOES NOT remove the element from its element flow* so the elements (second box) below it think that the element is still in its original location.
	- ![[attachments/Pasted image 20230317170949.png]]
- We can counteract by doing a margin-bottom of -40 px.

### `position: absolute`
- Original document for visual examples (second box):
	- ![[attachments/Pasted image 20230317171126.png]]
- This **removes the element from the normal element flow**.
- Removing the element from the element flow means *bottom elements won't account for the positioning of this element and will move up*.
	- ![[attachments/Pasted image 20230317171334.png]]
- It will **position the element with respect to the first ancestor that is NOT static** (absolute, fixed, relative, sticky).
- So if all the ancestor elements are static then it will be placed with respect to the root element which is the HTML document.
	- For `top = 0;` since `<section>` has a position of static we get this:
		- ![[attachments/Pasted image 20230317171832.png]]
- So if we make the second box position as relative then for the ribbon element the ancestor with non static positioning will be the second box.
	- ![[attachments/Pasted image 20230317173149.png]]

> [!note]- When you are using `position: absolute` focus on what the containing block (element with non-static position) you want it to be. You mostly never want it to be the root.
> In  the above example our containing block was second box.

### `position: fixed`
- The **element will be positioned relative to the browser window**.
- This **removes the element from the normal element flow**.
- So if we do a bottom 0 for footer it will stay at the bottom.
	- ![[attachments/Pasted image 20230317173423.png]]
- It will *stay fixed to the browser window* 0 pixels from the bottom.
	- ![[attachments/Pasted image 20230317173809.png]]

### `position: sticky`
- **Sticky element will never escape the content box (parent element)**. 
- The element is **positioned relative to the content box (parent element)**.
- *The height of the sticky element should never equal to height of the content box (parent element) otherwise it can never stick*.

> [!caution]- So for grids if you want a sticky title on the left and content on the right make sure to use `align-self: start` on the title.
> Example code pen: [position sticky - pushing things out of the way (codepen.io)](https://codepen.io/kevinpowell/pen/KEjMEv)
> ---
> Reference: [A couple of cool things you can do with CSS position sticky - YouTube](https://www.youtube.com/watch?v=8TyoihVGErI)
