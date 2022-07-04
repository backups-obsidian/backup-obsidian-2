---
created: 2022-06-28 19:23
updated: 2022-07-04 16:26
---
---
**Links**: [[107 Design Patterns Index]]

---
## SOLID Design Principles
- These were introduced by Robert C Martin (a.k.a Uncle Bob).

### Single Responsibility Principle (S)
> [!important]- Your package/struct/class should **have a single primary responsibility**

- Separation of concerns
- Putting everything in a single place is an anti-pattern and is also known as a God object/class.
- For **example** 
	- If you have a journal struct then it should only be responsible for adding, removing and listing entries from the journal
	- It should not be concerned with persistence like saving it to a file or uploading it somewhere. 
	- It should be handled by other functions or package. This way we are making sure we are not making a God Class/Object.

### Open Closed Principle (O)
> [!important]- It states that types should be **open for extension but closed for modification**. 
> - Enterprise Pattern : Specification
> - Illustration of Open Closed Principle is very well illustrated with the Specification pattern

- The specification pattern uses a bunch of interfaces

> [!tip] Try to remember the colour specification pattern.

### Liskov Substitution Principle (L)
