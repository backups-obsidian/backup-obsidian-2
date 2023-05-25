---
created: 2023-05-24 14:58
updated: 2023-05-25 09:27
---
---
**Links**: [[111 KodeCloud Index]]

---
## Network Namespaces
- When a *container is created we create a network namespace for it*.
	- Because of this it has **no visibility of network related information on the host**.
	- Within its namespace the *container can have its own virtual interface, routing and arp tables*.
	- Diagram:
		- ![[attachments/Pasted image 20230524153610.png]]
- Creating a network namespace: `ip netns add <namespace-name>`
- Listing the network namespaces: `ip netns`
- Viewing the interfaces inside the interface: `ip netns exec <namespace-name> ip link`
	- Or `ip -n <namespace-name> link `
- In the newly create network namespace we won't see other interfaces this means we have successfully prevented the namespace network from seeing the host network.
	- ![[attachments/Pasted image 20230524154228.png]]
	- Similarly we won't see any entries for arp (`ip netns exec <namespace-name> arp`) and route (`ip netns exec <namespace-name> route`)


## Recap
- [[KodeCloud - Networking Basics]]