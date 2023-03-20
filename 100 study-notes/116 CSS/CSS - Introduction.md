---
created: 2023-03-17 10:33
updated: 2023-03-19 18:53
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

> [!caution] Never use inline and internal. *Always go for external*.

## What is Cascading
- Cascading style sheets are called cascading because *several different style sheets can be active for a single document at the same time*. 
- Based on the rules it is up to the browser to decide which style will be applied to a particular element.
- Cascades follows a waterfall strategy. 
	- **The bottom one always overwrites the top one**.
 
```css
p {
	color: red;
}

p {
	color: yellow;
}
```

- In the above example colour of yellow will be applied to the paragraph element and NOT red because of cascading.
- **inline > internal | external** (Order of importance)
	- Here | means that internal or external can have preference over each other depending on which one is at the bottom.

- A *rule of thumb to avoid cascading from overwriting styles* is to have **more generic styles at the top** like p, h1, body etc and the **have more specific selectors like class or id selectors at the bottom**.

## Inheritance
- CSS follows inheritance so in the example below inheritance will be the following: `html -> body -> div -> p, h1`
	- So *if we change the font colour and set it to orange for the body then all the elements will use the same font colour*.

```html
<html>
  <head></head>
  <body>
    <div>
      <p></p>
      <h1></h1>
    </div>
  </body>
</html>
```

- An exception to this are the form elements which do not inherit these properties.
	- We need `font: inherit` to make them inherit the values from the parent.

> [!caution]- **NOT every property is inherited**. 
> If it is *related to typography then most certainly it will be inherited* if it is NOT related to typography there is a good chance that it WONT be inherited.

- With inheritance defining the right property at the right place can help us write very less CSS.
	- Setting up styles related to typography is always a good start.

 
> [!note]- We can use dev tools to understand why a particular style is not being applied to an element (like is it being overridden by some other style). 
> Dev tools follow inverse order (Top to bottom) when compared to cascading (Bottom to top) for showing which styles will be applied to an element.
> ---
> ![[attachments/Pasted image 20230319182043.png]]
