---
created: 2023-04-25 21:58
updated: 2023-04-26 12:22
---
---
**Links**: [[116 CSS Index]]

---
## Images
- When we upload an image to the website it has a default width and height which can be changed using CSS.
- In general we don't define a fixed height to an image since it might get cropped.
- We use `max-width` to keep images responsive.
- Fitting an image inside of a div

```jsx
 <div className="max-w-[600px] max-h-[600px] sm:h-[600px] sm:w-[600px] xl:mt-[126px] bg-pureBlack">
	<img
	  src={`data:image/jpg;base64,${img}`}
	  alt="PatientImage"
	  className="object-contain w-full h-full"/>
</div>
```

> [!note]- `object-contain` for the image will only work if we have defined height and width units. Not specifying them will result in `object-contain` not working.
> - The height and width can be in percentages but it must be specified.
> - `w-full` and `h-full` are important for the image.
