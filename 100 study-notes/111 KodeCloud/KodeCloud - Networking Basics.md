---
created: 2022-09-15 18:05
updated: 2023-05-24 16:09
---
---
**Links**: [[111 KodeCloud Index]]
**Recommended Reads**: [[../104 Linux/104 Linux Index#Networking | Linux Networking]]

---
## Networking
### Switching
![[attachments/Pasted image 20220915180827.png]]
- We can connect two computers using a switch
- To *connect these computers to a switch we need an **interface** on each host*.
	- This interface can be physical or virtual.

> [!important]- The very first requirement of assigning an IP address to a system is that it must have a physical or virtual interface.

- To see the **list of interfaces** on the host we use the `ip link` command.
	- **List the ip addresses** of the interfaces `ip addr`
- Assuming switch has an IP address of `192.168.1.0` we assign IP addresses to our interfaces. 
- Once the IP addresses have been assigned the computers can now connect with each other.

### Routing
- A router helps connect two networks.
	- ![[attachments/Pasted image 20220915181026.png]]

### Gateway
- We configure a gateway to let machine B know how to reach machine C.
- The system's need to know the address of the gateway.
	- This can be done using the `route` command. This will display kernel's routing table.
- Adding a gateway
	- ![[attachments/Pasted image 20220915181246.png]]
- Default gateway entry is used to route to the internet
	- Any request outside of your network goes to the default gateway.
	- Instead of default we can also use `0.0.0.0`
	- ![[attachments/Pasted image 20220915181518.png]]

> [!note]- Suppose if you have 2 different routers then you will need two different entries.
> ![[attachments/Pasted image 20220915181701.png]]

### Using Linux Host as a router
- How do we connect A to C
	- ![[attachments/Pasted image 20220915181837.png]]
- We add a routing table entry to A saying it has to go through B to reach C
	- `ip route add 192.168.2.0/24 via 192.168.1.6`
- Similarly we also need to let host C know that it can reach A using B.
	- `ip route add 192.168.1.0/24 via 192.168.2.6`

> [!caution]- This still won't be enough because *by default in linux packets are not forwarded*.
> This is for security reasons.
> Suppose your `eth0` is connected to internet and your `eth1` is connected to your private network. We don't want anyone from the internet to send packets to your private network.

- Whether a host can forward packets is governed by `/proc/sys/net/ipv4/ip_forward`
	- By *default value* in this file is set to *0*.
	- Setting this to 1 should enable packet forwarding.
- Just changing this won't persist the changes on reboot we need to also change `/etc/sysctl.conf`

### DNS
> [!note]- We can configure a custom DNS resolver by adding its entry to `/etc/resolv.conf`
> This is particularly useful when you have a lot of machines and you want to reference them using their names and don't want to modify each machine's `/etc/hosts` file.
> ![[attachments/Pasted image 20220915193336.png]]

- By default for DNS resolution the system first looks into the `/etc/hosts` file and then looks for the DNS resolver.
	- This order can be changed in `/etc/nsswitch.conf`
		- ![[attachments/Pasted image 20220915192607.png]]

- We can configure the custom DNS to forward all the requests to the public name servers (like google or Cloudflare) if the entry is not present in our custom DNS.
	- ![[attachments/Pasted image 20220915193041.png]]

> [!caution]- DNS troubleshooting tools like `dig` and `nslookup` doesn't consider the entries of `/etc/hosts` file.

#### Search domain 
- It is the domain name that gets appended.
	- ![[attachments/Pasted image 20220915193624.png]]
- The host is intelligent enough that if you type `web.company.com` then it won't append  `company.com` to it.

## Must Watch
- [Docker Networking Crash Course - YouTube](https://www.youtube.com/watch?v=OU6xOM0SE4o) - Great for understanding routing, DNS and docker networking.