---
created: 2023-05-15 12:25
updated: 2023-05-25 13:07
---
---
**Links**: [[104 Linux Index#Networking | Linux Networking Index]]

---
## Iptables
- Network traffic is made up of packets. 
	- *Iptables identifies the packets received and then uses a set of rules to decide what to do with them*.
- Iptables filters packets based on:
	- **Tables:** Tables are files that join similar actions. 
		- **A table consists of several chains**.
	- **Chains:** A chain is a **string of rules**. 
		- When a packet is received, iptables finds the appropriate table, then runs it through the chain of **rules** until it finds a match.
	- **Rules:** A rule is a *statement that tells the system what to do with a packet*. 
		- Rules can block one type of packet, or forward another type of packet. 
		- The outcome, where a packet is sent, is called a **target**.
	- **Targets:** A target is a decision of what to do with a packet. 
		- Typically, this is to accept it, drop it, or reject it (which sends an error back to the sender).
- Linux firewall iptables has *four default tables*.
	- Filter, NAT, Mangle, RAW
	- ![[attachments/Pasted image 20230525131031.png]]
- Iptables can be used to filter packets, route packets, etc.