---
created: 2023-03-22 10:44
updated: 2023-03-23 09:50
---
---
**Links**: [[116 CSS Index]]

---
## Overflows
- By default `overflow: visible`
- An example of overflow would be having a lot of text in a small box with width and height of 200px.
- We can hide the overflowing text using `overflow: hidden`.
- If we want a scroll bar we can use `overflow: scroll`.
	- The scroll bar will be around the 200 by 200px box.
	- `overflow: scroll` will always have scroll bars even if they aren't necessary.
		- ![[attachments/Pasted image 20230323093818.png]]
- `overflow: auto` will only have scroll bars if necessary.
	- ![[attachments/Pasted image 20230323093858.png]]
- We can control the behaviour of x and y scroll bars using `overflow-x` and `overflow-y`.
- **One of the main reasons for overflow is that the element has *both* a fixed width and height**.
- Some options of managing overflow are:
	- Use a scrollbar
	- Use `overflow-wrap: break-word;`, may not be very easy to read the word.
		- ![[attachments/Pasted image 20230323094339.png]]
	- Use a width of `min-content` to make the width as small as possible to fit the content.