---
created: 2023-03-17 16:23
updated: 2023-03-18 09:46
---
---
**Links**: [[116 CSS Index]]

---
## Box Model
- All HTML elements are a box.
- HTML elements consist of *border, padding, margins and content*.
	- All these affect the space an element takes up.
- **Padding and border is inside of the element** so adding it increases the size of the element.
- **Margin is outside of the element**.
- For using a border we need to specify style, radius and length.

> [!note] **Padding** is use to *add space around the content* (more background), **margin** is used to add space *between the components*.

- We can change the height and width of our content using `height` and `width` properties in CSS.
	- If we don't specify any height and width then content will consume the space depending on the content inside it.

- Box model diagram:
	- ![[attachments/Pasted image 20230318091902.png]]

> [!note]- In box model by default padding and border are added to the element.
> - If we set the `box-sizing: border-box` then **everything (padding, border) will add upto the specified width and height**.
> - So if the component has a height and width of 100px then adding a padding of 10px will make its height 120px and width 120px without `box-sizing: border-box`.
> - When using `box-sizing: border-box` to account for the padding and border such that they add to the given height and width of the element, height and width is subtracted from the content. 
> - Compare the image below with the image above. Height and width is the same in both, the only difference is that in the image show below I am using `box-sizing: border-box` which automatically shrinks the size of the content.
> ![[attachments/Pasted image 20230318092640.png]]

- We always use `box-sizing: border-box` as it makes styling so much easier since we know the exact size of the element when we specify using height and width and don't have to do the math concerned with padding and border.
	- If we feel that the content is not getting enough space, we can easily increase the height and width.
