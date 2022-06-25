---
created: 2022-06-25 14:54
updated: 2022-06-25 15:18
---
---
**Links**: [[107 Design Patterns Index]]

---
> [!question]+ What is OOP?
> Data (**fields**) + Behaviour (**functions**) related to data in a bundle (**objects**)

## Pillars of OOP
- There are 4 pillars of OOP

### Abstraction
- An `Airplane` class could probably exist in both a flight simulator and a flight booking application. 
- But in the former case, it would hold details related to the actual flight, whereas in the latter class you would care only about the seat map and which seats are available.
	- ![[attachments/Pasted image 20220625145528.png]]

> [!note]- Abstraction is representing all details relevant to the context with high accuracy and omitting the rest.

### Encapsulation
- Encapsulation is the **ability of an object to hide parts of its state** and behaviours from other objects, *exposing only a limited interface* to the rest of the program.
- To encapsulate something means to make it `private` , and thus accessible only from within of the methods of its own class. 

### Inheritance
- Inheritance is the ability to build new classes on top of existing ones. The main benefit of inheritance is *code reuse*.

### Polymorphism
- Polymorphism is the ability of a program to detect the real class of an object and call its implementation even when its real type is unknown in the current context.
- Using `interfaces`
