---
created: 2023-05-24 16:06
updated: 2023-05-29 13:27
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
- An entry in the route table has also been created once the interface is up:
	- ![[attachments/Pasted image 20230525162624.png]]

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

### Example: Creating a bridge network to connect the namespaces
- Creating 2 namespaces:
	- `ip netns add client`
	- `ip netns add server`
- Creating the bridge:
	- `ip link add v-bridge type bridge`
- Bringing the bridge interface up:
	- `ip link set dev v-bridge up`
- Creating `veth` interfaces for connecting the namespaces with the bridge network
	- `ip link add veth-client type veth peer name veth-client-br`
	- `ip link add veth-server type veth peer name veth-server-br`
- Connecting one of the `veth` interface to the namespace and other end to the bridge:
	- `ip link set veth-client netns client`
	- `ip link set veth-client-br master v-bridge`
	- `ip link set veth-server netns server`
	- `ip link set veth-server-br master v-bridge`
- Setting IP addresses for the `veth` interfaces:
	- `ip netns exec client ip addr add 192.168.15.1/24 dev veth-client`
	- `ip netns exec server ip addr add 192.168.15.2/24 dev veth-server`
- Bringing up the `veth` interfaces inside the namespaces:
	- `ip netns exec client ip link set dev veth-client up`
	- `ip netns exec server ip link set dev veth-server up`
- Bringing up the `veth` interfaces on the host machine:
	- `ip link set dev veth-client-br up`
	- `ip link set dev veth-server-br up`
- We *can now ping the namespaces from each other*.
	- Route table entries are created in both the namespaces
		- ![[attachments/Pasted image 20230525161024.png]]
- If we want to *ping the namespaces from the host then we need to assign an IP to the virtual bridge interface*.
	- `ip addr add 192.168.15.5/24 dev v-bridge`
	- When we give the virtual bridge and IP a route is automatically added in the route table of the host.
	- Diagram:
		- ![[attachments/Pasted image 20230525154023.png]]

#### Reaching the internet from inside the namespace
- We need to add a route table entry in the namespaces to use `192.168.15.5` (`v-bridge`) as the gateway.
	- `ip netns exec client ip route add default via 192.168.15.5`
	- `ip netns exec server ip route add default via 192.168.15.5`
	- ![[attachments/Pasted image 20230525161130.png]]
- We now need NAT to change the source IP of `v-bridge` to the IP of interface of the host (`eth0`) since the outside world won't know how to reply back if the source IP address of the packet is of `v-bridge` (it is a private IP address).
	- We do this by using the [[Linux - Networking - Iptables#SNAT in iptables | SNAT (source NAT) of iptables]].
	- `iptables -t nat -A POSTROUTING -s 192.168.15.0/24 -j MASQUERADE`
		- `MASQUERADE` will replace the source address of `v-net-0 (v-bridge)` with IP address of `eth0` as shown in diagram
			- ![[attachments/Pasted image 20230525160453.png]]
- We should now be able to ping internet from inside the namespaces.

> [!note] `v-bridge` is now acting as a router as it is connecting the namespace network with the outside network with the help of iptables NAT.

## Docker Networking (Network Namespaces)
- When docker is installed on a host the docker daemon **creates an internal private network named *bridge* by default**.
	- We can see the network using `docker network ls`
- The *name of the network is bridge* but on the host the network is created by the name of **`docker0`**.
	- We can see this from the output of `ip link`
		- ![[attachments/Pasted image 20230529112656.png]]
- Docker creates this network by running the `ip link add docker0 type bridge` command.

> [!note] A bridge network is like an *interface to the host* but a *switch to the namespaces/containers*.

- The `docker0` network is assigned an IP address of `172.17.0.1/16`
	- ![[attachments/Pasted image 20230529113835.png]]
- **Whenever a container is created docker creates a network namespace for it**.
	- We CANNOT the list the network namespace created by docker using `ip netns`.
	- Since `ip netns` command only lists the network namespaces present at `/var/run/netns` but the network namespaces for docker can be found at `/var/run/docker/netns`.
		- ![[attachments/Pasted image 20230529115228.png]]
	- So if we want to list the network namespaces create by docker we need to `ls /var/run/docker/netns`.

### How does docker attach the network namespaces to the bridge?
- If there are no containers running then the `docker0` bridge is in `DOWN` state.
	- ![[attachments/Pasted image 20230529115946.png]]
	- Apart from the `docker0` bridge interface there are no interfaces.

> [!important]+ **Once we run a container docker creates an interface of type `veth`**.
> - One end of the interface is attached to the `docker0` bridge and the other end is attached to the network namespace (container).
> ![[attachments/Pasted image 20230529120501.png]]

- *The `veth` interface connected to the network namespace (container) is assigned an IP address by docker*.
	- We can see this by doing an `ip addr` inside the container (network namespace)
	- ![[attachments/Pasted image 20230529121729.png]]
- Container as namespaces with `veth` interfaces connected to `docker0` bridge interface.
	- ![[attachments/Pasted image 20230529120825.png]]
- **In case of port mapping docker uses Iptables (NAT table) by creating rules to forward traffic**. 
	- Command used by docker for setting up the above rule: `iptables -t nat -A DOCKER -j DNAT --dport 8080 --to-destination 172.17.0.3:80`.
		- ![[attachments/Pasted image 20230529123037.png]]
		- ![[attachments/Pasted image 20230529123230.png]]

## References
- [Linux Network Namespaces with ip netns - YouTube](https://www.youtube.com/watch?v=iN2RnYaFn-0)
- [Network Namespaces Basics Explained in 15 Minutes - YouTube](https://www.youtube.com/watch?v=j_UUnlVC2Ss)
- Namespace written examples:
	- [Docker Networking Demystified (kubesimplify.com)](https://blog.kubesimplify.com/docker-networking-demystified)
	- [Container Networking Under The Hood: Network Namespaces | by adil | Medium](https://adil.medium.com/container-networking-under-the-hood-network-namespaces-6b2b8fe8dc2a) - how to connect to internet