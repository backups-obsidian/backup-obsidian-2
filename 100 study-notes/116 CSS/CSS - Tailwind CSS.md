---
created: 2023-03-20 10:47
updated: 2023-03-20 17:38
---
---
**Links**: [[116 CSS Index]]

---
## Tailwind CSS
- Tailwind is similar to inline CSS but we can have media queries in tailwind.
	- We CANNOT have media queries in simple inline CSS.
- We already have breakpoints predefined by tailwind.
	- ![[attachments/Pasted image 20230320131256.png]]
	- `md: text-green-100` means apply this style only when the screen size is greater than `md`. 
		- It is similar to `min-width` media query.
- We can customize tailwind using `tailwind.config.js`.
- If we want to add custom colours to tailwind then we have to use `tailwind.config.js`
	- `theme -> extend -> colors`

> [!note]- If we don't nest colours inside extend then it will overwrite all the colours.
> Extend is like append and it keeps the original configuration.

- `w-2/3` means a *width of 66%*.
	- `w-full` means 100%.
	- `w-auto` means auto width.
- For adding equal spacing using margins between *child elements* to separate them out we can use `space-y-2` (vertical spacing) or `space-x-2` (horizontal spacing).
	- This doesn't work well with grid and flexbox. 
		- Use gap in grid and flexbox.
- Responsive design: [Responsive Design – Tailwind CSS v2.0: From Zero to Production - YouTube](https://www.youtube.com/watch?v=hX1zUdj4Dw4&list=PL5f_mz_zU5eXWYDXHUDOLBE0scnuJofO0&index=4)

> [!note]- We can show and hide elements depending on our layout. For example there may be something that looks good in the desktop version but we may not want it in mobile.
> We can do it in tailwind using `<div class="hidden lg:flex">`
> So this particular `div` will be hidden for all breakpoints before `lg` and then become `flex` when the minimum width is `lg`.
> ---
> This is also useful for hamburger menus. When we get to a smaller screen size we would want our normal navigation div to disappear.
> `<div class="hidden md:block">`: once we reach to a screen size of `md` change its display from hidden to block.

- See the starting part of video on how to create a hamburger menu using React and tailwind: [React JS & Tailwind CSS Responsive Website - Beginner Friendly - YouTube](https://www.youtube.com/watch?v=ZU-drSVodBw)