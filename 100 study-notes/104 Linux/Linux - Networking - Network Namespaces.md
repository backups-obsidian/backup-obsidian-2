---
created: 2023-05-24 16:06
updated: 2023-05-25 12:44
---
---
**Links**: [[104 Linux Index#Networking | Linux Networking Index]]

---
## Commands
- Creating a network namespace: `ip netns add <namespace-name>`
- Listing the network namespaces:  `ip netns`
	- The namespaces are also listed at `ls /var/run/netns`
- Deleting network namespaces: `ip netns del <namespace-name>`
- To run commands in a particular network namespace:
	- `ip netns exec <namespace-name> <command>`
	- Example: `ip netns exec red ip link` - This will list all the interfaces in that particular namespace.

## Network Namespaces
- *Linux network namespaces are a Linux kernel feature allowing us to isolate network environments through virtualization*. 
	- **Each network namespace will have its own interfaces, routing tables and forwarding tables**.
		- ![[attachments/Pasted image 20230525124323.png]]
	- Used in *OpenStack*, Mininet, *Docker*, *kubernetes*, more...
- In linux there are *7 different types of namespaces* and NET namespace (network namespace) is one of them.
- When we create a new namespace it will *only have the loopback interface* and it *will be down* as a result this would mean that it won't have an IP address.
	- ![[attachments/Pasted image 20230525110615.png]]
- We can bring it UP using: `ip netns exec <namespace-name> ip link set dev lo up`. 
	- This will also **assign it an IP address** and we can use the ping command.
	- Commands screenshot:
		- ![[attachments/Pasted image 20230525111025.png]]
- Now *since the the network namespace is isolated and there are no routes I cannot ping anything on the internet*.
	- ![[attachments/Pasted image 20230525111222.png]]
- If we want to **connect 2 or more namespaces together (make them talk to each other) we can use virtual network interfaces**.
- We can *add virtual network interface* (`veth` - virtual ethernet) using:
	- `ip link add veth0 type veth peer name veth1`
	- They are **added in pairs** and are like a pipe.
	- They will be *added in the default/root namespace*.
	- They *won't have an IP address and will be down*.
	- Commands screenshot:
		- ![[attachments/Pasted image 20230525111648.png]]

> [!important] **`veth` is a type of virtual network interface** and there are a lot of other types like `bridge`, `tap` etc.

### Example: Creating 2 namespaces and connecting them using `veth`
- Creating 2 namespaces:
	- `ip netns add client`
	- `ip netns add server`
- Creating a virtual interface of type `veth` in the *root/default namespace*:
	- `ip link add pair-server type veth peer name pair-client`
- Setting the ends of the virtual interfaces in the namespaces:
	- `ip link set pair-server netns server`
	- `ip link set pair-client netns client`

> [!note]- The moment we connect the interfaces to the namespaces it disappears from the root/default namespace.
> - Name of virtual network interfaces after creating them in the default namespace
> ![[attachments/Pasted image 20230525120121.png]]
> - Name of virtual network interfaces after attaching them to one of the namespaces
> ![[attachments/Pasted image 20230525120503.png]]
> - Name of the virtual network interfaces from inside the namespaces
> ![[attachments/Pasted image 20230525120715.png]]
> ---
> - It is named as `pair-server@if17` since the server namespace DOESN'T know about the client namespace but *it knows that there is some interface*.

- We assign IP address to the virtual network interfaces:
	- `ip netns exec client ip addr add 10.0.0.1/24 dev pair-client`
	- `ip netns exec server ip addr add 10.0.0.2/24 dev pair-server`
- We bring up the virtual network interfaces:
	- `ip netns exec client ip link set dev pair-client up`
	- `ip netns exec server ip link set dev pair-server up`
- Now *we should be able to ping one namespace from another*.

> [!caution]- If only one of the virtual interfaces is up then it will show `LOWERLAYERDOWN`.
> ![[attachments/Pasted image 20230525121306.png]]

> [!note] If we have a lot of network namespaces then it will be a tedious process of creating `veth` and connecting them network namespaces. Instead we can use the a virtual network interface of type `bridge`.

### Example: Creating a namespace and connecting it with the host machine using `veth`
- Creating a namespace:
	- `ip netns add test`
- Creating a virtual interface of type `veth` in the *root/default namespace*:
	- `ip link add pair-test type veth peer name pair-host`
- Setting one end of the virtual interface in the namespace:
	- `ip link set pair-test netns test`
- We assign IP address to the virtual network interfaces:
	- `ip netns exec test ip addr add 10.0.0.1/24 dev pair-test`
	- `ip addr add 10.0.0.2/24 dev pair-host`
- We bring up the virtual network interfaces:
	- `ip netns exec test ip link set dev pair-test up`
	- `ip link set dev pair-host up`
- Now *we should be able to ping namespace from the host*.

## References
- [Linux Network Namespaces with ip netns - YouTube](https://www.youtube.com/watch?v=iN2RnYaFn-0)