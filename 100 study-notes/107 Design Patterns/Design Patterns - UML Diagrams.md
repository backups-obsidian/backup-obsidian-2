---
created: 2022-06-25 14:46
updated: 2022-06-25 15:16
---
---
**Links**: [[107 Design Patterns Index]]

---

> [!note]- Basic UML diagram
> ![[attachments/Pasted image 20220625144131.png]]

> [!note]- Class Hierarchy UML diagram
> ![[attachments/Pasted image 20220625144611.png]]
>
> Assuming that we have a related business requirement, we can go even further and extract a more general class for all living Organisms which will become a superclass for Animals and Plants. Such a pyramid of classes is a hierarchy. In such a hierarchy, the Cat class inherits everything from both the Animal and Organism classes.
>
> ![[attachments/Pasted image 20220625144738.png]]
>
> Subclasses can override the behaviour of methods that they inherit from parent classes. *A subclass can either completely replace the default behaviour or just enhance it with some extra stuff*.

> [!note]- Interfaces in UML Diagram
> ![[attachments/Pasted image 20220625151022.png]]
> 
> You could change the implementation of the `fly` method in these classes in any way you want. 
> As long as the signature of the method remains the same as declared in the interface, all instances of the Airport class can work with your flying objects just fine.

> [!note]- UML diagram of extending a single class versus implementing multiple interfaces at the same time.
> ![[attachments/Pasted image 20220625151535.png]]

- Arrows with *empty triangle heads and dashed lines* indicate that classes implement an *interface*.
	- Interfaces in UML look almost like classes, but only have methods.
- Arrows with empty triangles and solid lines show inheritance pointing from subclass to parent class.
- Simple arrow means one class depends on another