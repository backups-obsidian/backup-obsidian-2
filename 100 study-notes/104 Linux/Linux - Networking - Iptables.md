---
created: 2023-05-15 12:25
updated: 2023-05-29 13:24
---
---
**Links**: [[104 Linux Index#Networking | Linux Networking Index]]

---
## Commands
- List the chains in the default table: 
	- `iptables -L --line-numbers`
		- Line numbers are important when we have to delete a rule.
	- Verbose mode: `iptables -L -n -v`
- List the chains of a particular table: `iptables -t mangle -L -n -v`
- Syntax: `iptables -t <table-name> -options[chain] [matching component] [action component]`.
	- `iptables -A INPUT -s google.com -j DROP`: add a rule to drop packets coming from google.com in the INPUT chain.
	- `iptables -D INPUT 1`: delete rule 1 (line number) from the INPUT chain.
- Removing all the rules from the default chains:
	- `iptables -F`

## Iptables
- In linux operating system, the firewalling (filtering packets) is taken care of using **`netfilter`**. 
	- It is a kernel module that *decides what packets are allowed to come in or to go outside*.
	- **`iptables` are just the interface to `netfilter`**. 
- `ufw` uses iptables behind the scenes.
- Iptables are **made up of different tables**.
- Components of iptables:
	- **Tables:** Tables are files that join similar actions. 
		- **A table consists of several chains**.
		- Iptables has *three main tables*.
			- *Filter*: Main/**default table**, used for packet filtering
			- *NAT*: Used for network address translation.
			- *Mangle*: Used to modify the IP headers of the packets.
		- Chains for 3 main tables:
			- ![[attachments/Pasted image 20230525141536.png]]
	- **Chains:** A chain is a **string of rules**. 
		- Chains are *points in the route of the packet where we can apply rules*.
		- There are **5 chains** in Iptables: `PREROUTING`, `INPUT`, `FORWARD`, `OUTPUT` and `POSTROUTING`.
			- ![[attachments/Pasted image 20230525132020.png]]
			- All chains are NOT available for all tables.
	- **Rules:** A rule is a *statement that tells the system what to do with a packet*. 
		- Rules can block one type of packet, or forward another type of packet. 
		- The outcome, where a packet is sent, is called a **target**.
	- **Targets:** A target is a decision of what to do with a packet. 
		- Target component is triggered when the rule matches.
		- Typically, this is to accept it, drop it, or reject it (which sends an error back to the sender).
- *Policy is the default action* that is taken when none of rules match.
	- By **default Policy in all chains is to `ACCEPT`**.
- Iptables rules are **evaluated from top to bottom** so if we have a DENY at the top and then an ACCEPT at the bottom then the packet will be denied.
- Diagram for understanding the flow
	- ![[attachments/Pasted image 20230525140810.png]]

> [!caution] Iptables rules are ephemeral, which means they need to be manually saved for them to persist after a reboot.

- The difference between `DROP` and `REJECT` target is that *`DROP` DOES NOT notify the sender* about the dropped packet, which `REJECT` explicitly notifies the sender.

## NAT Table
- The _nat_ tables are used for network address translation, and it’s available in `PREROUTING`, `POSTROUTING`, and `OUTPUT` chains.
	- An incoming packet goes through the `PREROUTING` chain, and then the kernel makes the routing decision based on the routing table. 
	- If the *packet is destined for the machine itself, it’s put into a local process listening on the machine*. 
	- If it’s destined for another machine (so this machine serves as a router), the kernel will put it through the `POSTROUTING` chain and then send it to an output interface.
	- A packet generated from a local process will be routed and then put into the `OUTPUT` chain. The packet then goes to `POSTROUTING` and then goes to an output interface.
- Chain diagram:
	- ![[attachments/Pasted image 20230525143940.png]]

### SNAT in iptables
- Reference network diagram:
	- ![[attachments/Pasted image 20230529130022.png]]
- To **access the Internet from the two computers in the local network** (see reference diagram), the following **SNAT** (S stands for _source_) rule has to be added to the router: 
	- `iptables -t nat -A POSTROUTING -o eth0 -s 192.168.1.0/24 -j SNAT --to-source 50.60.70.80`
		- Here `-t` stands for _table_, `-A` stands for _append_, `-s` stands for _source_, `-o` stands for _output interface_ and `-j` stands for _jump_. 
	- This rule will *replace the source address of any IP packets going to the Internet through interface `eth0`* whose source address is in our local subnet with `50.60.70.80`, the public IP address of the router. 
	- For any response packets, the reverse operation that sets the destination address to the private address of the connecting device (e.g., 192.168.1.2) is automatically applied, so we do not need to worry about configuring it.
	- Diagram after adding the rule:
		- ![[attachments/Pasted image 20230529130333.png]]
	- Our router may be allocated a *dynamic address by DHCP* instead of being configured a static one. 
		- In this case, we could use the *`MASQUERADE` target, which is similar to SNAT except that we do not need to specify `--to-source` address*. 
		- Instead, the IP address of the output interface (here, `eth0`) will be used automatically.
		- `iptables -t nat -A POSTROUTING -s 192.168.1.0/24 -o eth0 -j MASQUERADE`

> [!important] SNAT iptables are also used when we want to access [[Linux - Networking - Network Namespaces#Reaching the internet from inside the namespace|  internet from network namespaces]].

### DNAT in iptables
- Another use case of NAT is _port forwarding_. 
- When we are *running a service on one of our computers*, say, PC1, and **would like to access it from the Internet**. 
- **Port forwarding is usually implemented using DNAT** (D stands for _destination_) rules
- Suppose the service is opened on port 80 and we want to access it on port 8080 of the router. We will add the following DNAT rule.
	- `iptables -t nat -A PREROUTING -p tcp -m tcp --dport 8080 -j DNAT --to-destination 192.168.1.2:80`.
	- The option `-p tcp` (`p` stands for _protocol_) matches for TCP connections (so this rule doesn’t apply to UDP), while `--dport 8080` (`--dport` stands for _destination port_) matches for TCP traffic destining port 8080. 
	- *This rule will replace the destination address of any IP packets going to port 8080 with 192.168.1.2, the IP address of the service*. 
	- *The port is also replaced with 80, the port of the service*. 
	- For any response packets, the reverse operation is also automatically applied, just like in SNAT. The process works like the diagram below.
	- Diagram after adding the rule:
		- ![[attachments/Pasted image 20230529131131.png]]

> [!important]- If you try to access the service from the router itself using `127.0.0.1:8080`, you will find that it DOESN'T work. 
> - Note that our command puts the *DNAT rule into the `PREROUTING` chain*, **but packets generated by local processes go to the `OUTPUT` chain** instead of `PREROUTING`. 
> - As a result, we have to also configure DNAT on the `OUTPUT` chain for a local process to access the service.
> - `iptables -t nat -A OUTPUT -p tcp --dport 8080 -j DNAT --to-destination 192.168.1.2:80`

### Example: Port Redirection
- We have a nodejs application running on port 8080 but we want to access it using port 80. 
	- We will have to add a rule to the `PREROUTING` chain.

```bash
iptables --table nat --append PREROUTING --protocol tcp --dport 80 --jump REDIRECT --to 8080
```

- In port redirection we can only change the port.

### Example: Forwarding (DNAT)
- We can **change both the port and the IP**.
	- This can *also be used for redirection*.
- **INCOMPLETE**: [iptables Demystified - Port Redirection and Forwarding HTTP Traffic to another machine (part 1) - YouTube](https://www.youtube.com/watch?v=NAdJojxENEU)

## References
- [Iptables for Routing (stackunderflow.dev)](https://stackunderflow.dev/p/iptables-for-routing/#snat-in-iptables)
