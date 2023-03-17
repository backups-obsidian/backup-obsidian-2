---
created: 2023-03-17 10:33
updated: 2023-03-17 10:52
---
---
**Links**: [[116 CSS Index]]

---
## Different ways of applying css
- **Inline style**:
	- Using the `style` tag.
	- `<body style="background-color: tomato;">`
- **Internal style**:
	- We write the styles inside the *`head` tag of html*.

```html
<head>
  <style>
    body {
      background-color: black;
      color: white;
    }
  </style>
</head>
```

- **External style**:
	- Separate css document that we can reference in the *`head` of the html*.

```html
<head>
  <link rel="stylesheet" href="style.css" />
</head>
```


> [!question]+ Why is CSS called cascading style sheets?
> Cascading style sheets are called cascading because *several different style sheets can be active for a single document at the same time*. 
> Based on the rules it is upto the browser to decide which style will be applied to a particular element.
> ---
> Cascades follows a waterfall strategy. **The bottom one always overwrites the top one**.
>
> ---
> **inline > internal | external** (Order of importance)
> Here | means that internal or external can have preference over each other depending on which one is at the bottom.

> [!caution] Never use inline and internal. *Always go for external*.