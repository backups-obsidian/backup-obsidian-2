---
created: 2023-03-21 13:18
updated: 2023-03-21 14:38
---
---
**Links**: [[116 CSS Index]]

---
## Z Index
- By default in HTML the *further down you are greater is the stacking order*.
	- This means you have the lowest stacking order at the top.
- Example:
	- ![[attachments/Pasted image 20230321135013.png]]
	- Now *if we want b to show over d we would use z-index*.
	- If we want to show a above all the elements then we can use negative z-index for b,c,d.
- By default all the elements have a z index of **auto (0)**.
- *Higher the Z index, greater is the stacking order*.
- We use z index to determine the *depth of elements* in CSS.

> [!caution]- Z index **only works for if the element has a NON static position**.
> Just like left, right, top, bottom to work the element *must have a non static position value*.

```css
/* WRONG: No position, z index won't work */
.some-class {
	margin: auto;
	z-index: 1;
}

/* CORRECT */
.some-class {
	margin: auto;
	z-index: 1;
	position: relative;
}
```

- We create a **new stacking context when we apply a z-index value to an element**.

> [!important]- When we give a *z index to an element we give it with respect to a stacking context*.
> - Z index of the parent element (stacking context) always applies to its children.
> - So if the parent (stacking context) has a lower z index and child has a higher z index, the child will inherit the lower z index from the parent.
> ---
> ![[attachments/Pasted image 20230321141120.png]]
> - Here the z index of the yellow box is with respect to its parent (red box) and the z index of the blue box is with respect to the body. 
> - Since both of them are different contexts blue box is over yellow box even after having a lower  z index.
> - If the red box had a z index greater than the blue box then the yellow box would have been above the blue box.

- `transform` and any `opacity` < 1 also creates a stacking context.
- Grid and flexbox also create a stacking context.

## Summary
- Elements in the *same stacking context* will **display in order of appearance**, with latter elements on top of former elements.
- Elements need to **have their position set to anything other than , "static"** in order for z-index to do anything.
- **Positioned elements will display on top of un-positioned elements** even without a Z-Index.
- Some CSS properties like *"opacity" and "transform" will put the element in a new stacking context*.
- The **element's z-index may be unintentionally limited by its parent's z-index value**.

## References
- [CSS: 4 Reasons Your Z-Index Isn't Working - YouTube](https://www.youtube.com/watch?v=qYi-OLf5q5g)
