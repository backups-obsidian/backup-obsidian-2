---
created: 2023-03-17 10:54
updated: 2023-03-17 11:31
---
---
**Links**: [[116 CSS Index]]

---
## Properties & Values
- *Combination of property and value* is known as a CSS **declaration**.
- The `font-size` *property* can take these types of *values* `small`, `120px`, `100%`
	- Not all property types can take these types of values.

## Selectors
- Sample HTML document for understanding selectors

```html
<html>
  <head>
    <link rel="stylesheet" href="style.css" />
  </head>
  <body>
    <h1>CSS Vocabulary</h1>
    <section>
      <b>CSS Rule:</b>
      <p>
        A <b>CSS Rule</b> CSS rule-set consists of a selector and a declaration block:
      </p>
    </section>
    <div>
      <b>CSS selectors:</b>
      <p>
        <b>CSS selectors</b> are used to "find" (or select) HTML elements based
        on their element name, id, class, attribute, and more.
      </p>
    </div>
  </body>
</html>
```

- Different CSS selectors

```css
/* Selecting only b */

b {
 font-size: 100%;
}

/* Selecting only b that are the descendants of section */
section b {
 font-size: 100%;
}

/* 
Selecting only the direct children b of section. This would exclude any grand children. In the above example 

A <b>CSS Rule</b> CSS rule-set consists of a selector and a declaration block:

Is the grandchildren b of section. Hence it would NOT be affected by this style.
*/
section > b {
 font-size: 100%;
}

/* We can use , to combine selectors to have the same declaration */
section > b, div > b {
 font-size: 100%;
}
```

- It is easier to use class names and ids instead of creating these complex selectors.

### Class & ID selectors
- Classes are used if we want to **apply the same style to a lot of elements**.
- We declare a class using the `class` keyword: `<h1 class="box">`
- We select a class using a period: `.box { ... }`
- We use IDs if we want to apply a **style only to a single element**.
	- An ID name is *only supposed to be used once* in a document so that it can be uniques.
- We declare a class using the `id` keyword: `<h1 id="box-id">`
- We select a class using a pound: `#box-id { ... }`

> [!question]- When should we use classes?
> The rule of thumb for using classes are:
> - When you want to *apply the same style to multiple elements*.
> - But *NOT all elements are of the same name*
> ---
> A **class can be used on multiple elements**, and an **element can have multiple classes**.

> [!question]- When should we use IDs?
> Here are two rules of thumb
> - When you *don't need to apply the same style to multiple elements*.
> - To *have a unique selector to a common element*.
> ---
> A **ID CANNOT be used on multiple elements**, and **an element CANNOT have multiple IDs**.

- We can combine classes and ids with the above selector rules
	- Example: `#box-id > P > b`: bold elements that are the children of paragraph elements that are children of the element with the id of box-id.