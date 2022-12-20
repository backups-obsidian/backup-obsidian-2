---
created: 2022-12-18 19:46
updated: 2022-12-18 20:07
---
---
**Links**: [[108 Python Index]]

---
- A function should perform one thing and do it well.
	- *Things that a function does should be in the same level of abstraction*.
- Don't pass complete objects to functions for performing operations on them. Pass the parameters on which you want to apply the function.
	- For example consider that you have class called Person and it has attributes firstname and lastname. Now you have created a function that converts the name to uppercase then it is best to pass the function attribute firstname of the object instead of passing the whole object and then accessing the name from the object.
	- We can summarise this as: **a function should only request the information it needs**.
- *To make function calls clearer use keyword arguments*. 
	- You can force keyword arguments in a function.
- Keep the number of function parameters to minimum.
	- The number of parameters you pass to a function is a good indication of what the function is trying to do.
- **Don't create and use an object in the same place**.
	- ![[attachments/Pasted image 20221218195853.png]]
	- This makes the code harder to test.
	- ![[attachments/Pasted image 20221218195952.png]]
- When you see boolean flags then it means that the function should be split into 2.
	- This is generally only possible if the else condition is doing something completely different.

> [!tip]- If the function has and in the name like `create_user_and_store_in_db` then it is a red flag that the function is doing too much and it should be broken into multiple functions.

- *Function names should be verbs and arguments should be nouns*.

## References
- [The Ultimate Guide to Writing Functions - YouTube](https://www.youtube.com/watch?v=yatgY4NpZXE)