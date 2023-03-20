---
created: 2023-03-17 11:32
updated: 2023-03-19 18:37
---
---
**Links**: [[116 CSS Index]]

---
## Specificity
- Specificity is how the browser knows which rules/declarations are most important to be applied to an element.
- If there are multiple rules with the same specificity then one at the bottom will be applied.
- **With specificity we can counteract effects of the cascade**.
	- In the example below although `.class` is below `html .class` but `html .class` will be used to determine the style.

```css
#id {
  background-color: blueviolet;
}

/* html .class will be applied over just .class since it has 2 levels of specificity */
html .class {
  background-color: tomato;
}
.class {
  background-color: goldenrod;
}

/* html body will be applied over just body since it has 2 levels of specificity */
html body {
  background-color: aqua;
}
body {
  background-color: goldenrod;
}
```

> [!note]- Order of specificity: *inline > `id` > `class` > normal selector*.
> In CSS `id` is of the highest specificity and only inline style can override an ID.

- Specificity calculator: [Specificity Calculator (keegan.st)](https://specificity.keegan.st/)

- If you are having trouble finding out why a particular style is not being applied to an element always use dev tools.
	- ![[attachments/Pasted image 20230319183726.png]]