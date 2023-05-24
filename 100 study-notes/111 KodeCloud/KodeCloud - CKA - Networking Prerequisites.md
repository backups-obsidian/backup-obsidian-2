---
created: 2023-05-24 14:58
updated: 2023-05-24 16:05
---
---
**Links**: [[111 KodeCloud Index]]

---
## Networking Commands (THIS SHOULD NOT BE HERE)
- *List the interfaces* on the host: `ip link`
- *See the IP address assigned to the interfaces*: `ip addr`
- *Set IP addresses on the interface*: `ip addr add 192.168.1.12/24 dev eth0`
	- These are *valid only till a restart*.
	- If we want to persist these changes we have to change the network interfaces file in the `/etc` directory.
- *View the routing table*: `ip route` or `route`
- *Add entries into the routing tables*: `ip route add 192.168.1.0/24 via 192.168.2.1`.
- Check if IP forwarding is enabled on the host: `cat /proc/sys/net/ipv4/ip_forward`
	- **1** means it is **enabled**.

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