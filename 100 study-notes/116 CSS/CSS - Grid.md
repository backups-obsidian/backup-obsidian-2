---
created: 2023-03-18 15:45
updated: 2023-03-19 11:12
---
---
**Links**: [[116 CSS Index]]

---
## Grid
- Just like in flexbox we have the concept of grid container and grid items.
- Grid also applies to its **direct children**.
- We declare a container as a grid container using `display: grid`.

- If we want to have 3 columns and 2 rows
```css
.container {
	display: flex;
	grid-template-columns: auto auto auto;
	grid-template-rows: auto auto;
	grid-gap: 5px;
}
```

- The grid is responsive since rows and columns are set to auto.
	- We can also declare them to be of fixed width.
- If we want the grid to grow and shrink unequally we use fraction units.

- In the below example 2nd column will have twice the width of 1st and 3rd column.
```css
.container {
	display: flex;
	grid-template-columns: 1fr 2fr 1fr;
	grid-template-rows: auto auto;
	grid-gap: 5px;
}
```

> [!note] Always use `grid-gap` for separating grid items instead of margins.

- We can also use repeat if all the columns have to be of the same width. 
	- For example if there are 3 columns with 1fr each then we can use `repeat(3, 1fr)`
- `grid-template` is shorthand for `grid-template-rows` and `grid-template-columns`.
	- `grid-template: repeat(3, 1fr) / repeat(2, 1fr)`

> [!note]- In most cases we would need to only declare `grid-template-columns` unless the rows are of unequal height. In that case we would need to specify `grid-template-rows` also.
> This works because **rows are implicitly created for us by grid**.

- For arranging the items in the grid we use `grid-column` and `grid-row`
	- Example: `grid-column: 1 / 3`
	- We can use -1 as the ending value if we want our elements to span till the end of the grid.
	- If we don't know their starting point and know that they should only occupy 2 spaces then we should use `grid-column: span 2`
	- `grid-column: 2` means take the 2nd grid it is same as `grid-column: 2 / 3`
 
- Example layout using grid:
	- ![[attachments/Pasted image 20230318160744.png]]
- We can also use `grid-template-areas` for quick prototyping.
	- It is useful for positioning the items.
	- We declare the grid using `grid-template` only, we just position the grid elements using `grid-template-areas`

- Example:
	- ![[attachments/Pasted image 20230318161635.png]]

> [!important]- Grid template areas are great for building responsive layouts.
> We don't have to work with `grid-column` numbers and can easily create many different layouts.
> ![[attachments/Pasted image 20230318181139.png]]
> ---
> - In the example above the number of columns are always changing. If we want all the created columns to be of the same width we can use `grid-auto-columns: 1fr`.
> - This means any automatically (*implicit*) generated column  will be of 1fr.
> - We can do the same with automatically (*implicit*) generated rows using `grid-auto-rows: 1fr`

- Responsive grids using `minmax` and `autofit`
	- These *won't be needed in most cases* and media queries will work just fine.

```css
.container {
  display: grid;
  grid-gap: 5px;
  grid-template-columns: repeat(auto-fit, minmax(100px, 1fr));
  grid-template-rows: repeat(2, 100px);
}
```

- With the above logic we will have **implicit rows** (we have defined only 2 rows) created when the screen shrinks.
	- We can fix this using `grid-auto-rows: 100px`

- We can position the grid elements using `justify-content` (*horizontally*) and `align-content` (*vertically*) provided they have space (not using fractional units).
	- The default values of `justify-content` and `align-content` are stretch.

> [!important]- The main difference between flexbox and grid is that *flexbox* is built for a **1-D**  layout whereas *grid* is build for **2-D** layout.
> - Flexbox is great for aligning within the container.
> - For a more overall layout like a page we should use grid.

> [!note]- Another main difference is that **flexbox is content first** whereas **grid is layout first**.
> - In case of flexbox content makes the decisions where they are going to be placed.
> - In case of grid we explicitly define where each element should go.

> [!note]- In *grid* **parents have more control over the layout and children/siblings don't influence the layout** whereas in *flexbox* **children/siblings have influence the layout**.
> This can make designing UI unpredictable sometimes when using flexbox.


- An example of combining grid with flexbox would be designing the page layout using grid and using flexbox for the nav.
- Another example of using flexbox and grid together
	- ![[attachments/Pasted image 20230319110918.png]]
	- The cards are laid out using grid whereas the tags inside the cards are laid out using flexbox.
	- Flexbox works the best for tags *since we want to rely on the intrinsic sizing of the tags*. 

## Summary
|      Property       | Location  |
|:-------------------:|:---------:|
|    grid-template    | container |
|     grid-column     |   items   |
|      grid-row       |   items   |
| grid-template-areas | container |
|      grid-area      |   items   |
|   grid-auto-rows    | container |
|   justify-content   | container |
|    align-content    | container |
|     align-self      |   items   |
|    justify-self     |   items   |

## References
- [Learn CSS Grid the easy way - YouTube](https://www.youtube.com/watch?v=rg7Fvvl3taU)