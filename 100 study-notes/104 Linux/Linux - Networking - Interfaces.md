---
created: 2023-05-24 16:10
updated: 2023-05-25 15:54
---
---
**Links**: [[104 Linux Index#Networking | Linux Networking Index]]

---
## Commands
### Interfaces
- *List the interfaces* on the host: `ip link`
- *See the IP address and other details of the interfaces*: `ip addr`
- See the *IP address and other detail of a particular interface*: `ip addr show <interface-name>`
- *Set IP addresses on the interface*: `ip addr add 192.168.1.12/24 dev eth0`
	- Here `dev` stands for device.
	- These are *valid only till a restart*.
	- If we want to persist these changes we have to change the network interfaces file in the `/etc` directory.
- Get *statistics of each interface*: `ip -s link`
	- To see the statistics of a particular interface: `ip -s link show <interface-name>`
- We can *bring interfaces up/down* using the following commands:
	- `ip link set <interface-name> up`
	- `ip link set <interface-name> down`

### Routes
- *View the routing table*: `ip route` or `route` (gives output in a tabular format)
- *Add entries into the routing tables* 
	- `ip route add 192.168.1.0/24 via 192.168.2.1`
- *Delete entries* from the routing table
	- `ip route delete 192.168.2.1/23 via 10.11.12.3`
- Adding and deleting a route can also be performed using the `route` command.

#### Understanding the output of route command
![[attachments/Pasted image 20230525155117.png]]
- Here **gateway is the router**. 
- The 4 0's is a special value, it is like a * which means it matches any value. 
- The routing tables uses the longest prefix match so the closest match of the destination from these entries helps decide where to go. 
	- *NOT dependent on the order*.
- *If you want to go to any destination within the network `192.168.1.0` you can find this within this network, `0.0.0.0` in the gateway means no need to use the router send directly using the lan connection*. 
	- You can also verify this using the `arp -n` command to see the computers to which you can send packets without going through the router.

### Miscellaneous
- *View the arp cache*: `arp`
- Check if IP forwarding is enabled on the host: 
	- `cat /proc/sys/net/ipv4/ip_forward`
	- **1** means it is **enabled**.

## Interfaces
- **A network interface is the point of connection between a computer and a network**. 
	- A network interface is *how the kernel links up the **software side** of networking to the hardware side*.
- The Linux system distinguishes two types of network interfaces – the *physical network interface* and the *virtual network interface*.
	- A **physical network interface** represents a network **hardware device** such as *NIC* (Network Interface Card), *WNIC* (Wireless Network Interface Card), or a *modem*.
	- A **virtual network interface** *DOES NOT represent a hardware device but it is usually linked to one*.
		- There are *different kinds of virtual interfaces* e.g. **Loopback**, **bridges**, **VLANs**, *tunnel interfaces* and so on
- Linux systems use *two different styles of naming the network interfaces*. 
	- The first style is the *old-style* name, such as *eth0, eth1, and wlan0*. 
	- The *new ones* are based on hardware locations like _enp3s0_ and _wlp2s0_.
- A machine **can have multiple interfaces** which means it can **have multiple IP addresses**.
	- This means the machine can *participate in many networks* and have an IP address for each network.
		- ![[attachments/Pasted image 20230525080401.png]]
- Generally multiple interface means that the computer has an multiple NIC (network interface cards a.k.a network adapters).
	- NICs are integrated in the laptop but incase of a PC it might not come with a NIC and we would have to buy one and attach to the motherboard.
	- NICs use PCIE slots.
- When you *run a server or a program* by default it **runs on a particular network interface**.
	- This means the program or server will be able to accept connections from only that network.
	- Example if we have a nodejs server running on the blue network then it won't be accessible from the green network or the yellow network.
		- ![[attachments/Pasted image 20230525080720.png]]
	- If we choose `0.0.0.0` then our app will listen to all the interfaces.
	- We can find the interface our app is listening to using `netstat -tpln`
- `ip link` command also shows us *which interfaces are up* (kernel level).
	- ![[attachments/Pasted image 20230525081513.png]]
	- `LOWER_UP` means that we have established connection at the physical level.
- The second line for each interface shows the link layer address associated with the device.
	- ![[attachments/Pasted image 20230525091151.png]] 

## References
- [Linux Network Interfaces— Minimal Know how… helped me a lot .. | by Amod Kadam | Medium](https://amod-kadam.medium.com/linux-network-interface-s-minimal-know-how-helped-me-a-lot-168547b471d9)