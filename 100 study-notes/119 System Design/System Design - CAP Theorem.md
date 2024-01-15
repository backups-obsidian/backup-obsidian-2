---
created: 2024-01-13 16:47
updated: 2024-01-13 17:06
---
---
**Links**: [[119 System Design Index]]

---
## CAP Theorem
- **CAP** stands for **Consistency** **Availability** and **Partition tolerance**.
	- *Consistency means the system returns the same data to all the clients*.
	- Availability means the system is able to respond to requests.
	- *Partition tolerance* means the ability of a system to keep functioning even if there is a *network partition (nodes/machines in the system can't reach each other)*.
- **CAP theorem states that during a network partition a distributed system must choose between consistency and availability**.
	- *The system has to decide between pausing the traffic and losing availability or continue accepting the traffic and losing consistency*.
	- For example we can consider 2 ATM machines connected to each other forming a system. Now for some reason there was a network partition and the ATM machines can't reach each other. 
		- Now they must decide between availability (people being able to withdraw balance from it) and consistency (showing the same balance on both the ATMs). 
		- If the ATMs decide to choose consistency then we cannot draw money from either of the ATMs leading to unavailability. 
		- If we choose availability then since the ATMs are not connected they will have different user balances depending on which ATM was used to withdraw money.
		- So in this example it makes sense to prefer *consistency over availability*.
	- In case of a social media application where multiple users are writing and reading comments it makes sense to prefer *availability over consistency*.