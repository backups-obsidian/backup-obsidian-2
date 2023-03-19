---
created: 2023-03-18 09:38
updated: 2023-03-19 11:00
---
---
**Links**: [[116 CSS Index]]

---
## Flexbox
- *Navigation bars* are the perfect use case for a flexbox.
- In flexbox we have the concept of containers and items.
	- **Direct children** of *flex containers are known as flex items*.

- Example (direct children): A navigation bar using `display: flex` for class `navbar`. 

```html
<!-- This is WRONG -->
<!-- Having a display of flex for class navbar won't do anything to the li items since they are not direct children of the flexbox container (<nav>)  -->
<nav class="navbar">
  <ul>
    <li>Home</li>
    <li>About</li>
    <li>Search</li>
    <li>Logout</li>
  </ul>
</nav>

<!-- This is CORRECT -->
<!-- Here display of flex for class navbar will work since li elements are the direct children ofr the flexbox container (<ul>)  -->
<nav>
  <ul class="navbar">
    <li>Home</li>
    <li>About</li>
    <li>Search</li>
    <li>Logout</li>
  </ul>
</nav>
```

- To use a flexbox layout the container needs to have a `display: flex`

> [!important]- When we do `display: flex` the children block elements actually become flex items. They are no longer block elements.
> All the flex items *line up horizontally by default* even if it means there can be overflow (horizontal scrollbar) for small screen sizes.

- The *default behaviour of flex item* is to be **as small as possible while maintaining everything on one line**.

- The *default flex box direction is from left to right* (`flex-direction: row`). 
	- This is also known as the **main axis** (along the row).
	- We also have a **cross axis** (top to bottom).
 
> [!note]- We use different CSS properties to position the content along the main axis and along the cross axis.

- We can flip the direction of flex box such that main axis is from top to bottom and cross axis is from left to right using `flex-direction: column`.
	- Now *elements will be stacked vertically*.
	- We will mostly use the default `flex-direction` i.e. row.
- `justify-content` controls the positioning of elements along the main axis.
	- The default is `justify-content: flex-start`
 
> [!note]- `justify-content` will only change the position of flex elements if there is space to do so. 
> So if any of the elements have `flex: 1` then there would be no space (since `flex-grow: 1`) and `justify-content` would be useless.

- `flex-direction`, `justify-content` and `display: flex` are all **properties of the flex container**.
- If we want to position an individual item to the right we can use `margin-left: auto` for that *flex item*.

- This layout can be achieved in 2 different ways:
	- ![[attachments/Pasted image 20230318101623.png]]
	- Here home, logout and search are the direct children of flexbox container.

```css
/* Way one*/
.container {
  display: flex;
  justify-content: flex-end;
}

.home {
  margin-right: auto;
}

/* Way two */
.container {
  border: 5px solid #ffcc5c;
  display: flex;
  justify-content: flex-start;
}

.search {
  margin-left: auto;
}
```

- If we want the flex items to grow and shrink with the size of flex container we give the items `flex: 1`.
	- In general we only specify it for one of the items since we don't want all the items to grow and shrink with the container.
	- Like in a navbar we only want the search to grow.
- When we give a `flex: 1` to any of the flex items then it takes rest of the space push other elements to the end.
- `flex: 1` is a short hand for `flex-grow: 1`, `flex-shrink: 1` and `flex-basis: 0`.

> [!note]- If we want all the elements to be equally spaced then all the flex items should have `flex: 1`
>```css
>.flex_container > * {
>	flex: 1;
>}
>```

- The *items stretch themselves across the cross axis*.
	- By default `align-items: stretch`
- Flex box is great for centring an item inside a container using `align-items: center` and `justify-content: center`
- Flex box won't allow us to set the width explicitly if there is not enough width in the container itself to fill that content.
- By default `flex-wrap: nowrap`
	- But we can have it wrap by setting it to wrap.
- We can change ordering using the `order` property
	- By default `order` is set to 0 so elements with value greater than 0 will be placed to the right and elements with value greater than 0 will be placed to the right.

## Summary
| Axis  |    Property     | Location  |
|:-----:|:---------------:|:---------:|
| Main  | justify-content | container |
| Main  |     Margin      |   items   |
| Main  |      flex       |   items   |
| Main  |    flex-wrap    | container |
| Main  |      order      |   items   |
| Cross |   align-items   | container |
| Cross |   align-self    |   items   |

