---
created: 2022-11-12 09:29
updated: 2022-11-12 22:57
---
---
**Links**: [[300 home]]

---
## Why use VPN?
### Access to private subnet of a  company
- Let us consider a scenario we have some important files in the private subnet of our company. This private subnet does not have access to internet and files can only be accessed from within the office. Now if we want to access those files urgently we have to use a VPN.
	- VPN gives us an IP address of office internal network with which we can access the private subnet.
	- The VPN server has to be installed on the public subnet so we can access it from anywhere.
	- ![[attachments/Pasted image 20221112093754.png]]

### Changing IP address
- Flow without VPN
	- ![[attachments/Pasted image 20221112100533.png]]
- Flow with VPN
	- Each VPN server will have a public IP address which we will use to connect to it.
	- It will be shared with all the users using that VPN.
	- When we initiate a connection to the server OpenVPN will create a virtual network card on our device and ask the VPN server to get a virtual private IP address.
	- Now instead of using the router as a default gateway it will use the VPN as a default gateway.
	- It is upto the client to decide whether to route all the requests through VPN or only a subset of requests based on the target's IP address.
	- ![[attachments/Pasted image 20221112101141.png]]
- The VPN client can be installed on the client's machine or on the router.
	- If it is installed on the router then all the devices connecting through the router will automatically use the VPN.

> [!note]- All VPN servers know and keep track of our public IP addresses (address of the router)

## OpenVPN
> [!question]+ What is OpenVPN?
> - **OpenVPN is both a VPN protocol and software** that uses VPN techniques to secure point-to-point and site-to-site connections. 
> - Currently, it’s one of the most popular VPN protocols among VPN users.
> - Programmed by James Yonan and released in 2001, OpenVPN is one of the only open-source VPN protocols that also has its own open-source application.
> - OpenVPN doesn’t have any support for L2TP, IPSec, and PPTP, it uses its own custom protocol based on TLS and SSL.

- OpenVPN can use either TCP or UDP.
- OpenVPN uses OpenSSL for its VPN connections
- *1194 is the OpenVPN port*.
	- You can use port 443 if you using OpenVPN over UDP, it won't conflict with HTTPS (port 443) since it uses TCP.
- Other VPN protocols:
	- *WireGuard*: Comparable to OpenVPN.
	- *PPTP* (Point-to-point tunneling protocol): Insecure as compared to OpenVPN
	- *L2TP/IPSec* (Layer 2 tunneling protocol): Natively available on most platforms. L2TP on its own offers 0 encryption. That’s why it’s always paired up with IPSec
	- *IKEv2/IPSec* (Internet key exchange version 2)

- The average user ends up using the OpenVPN protocol through a separate VPN provider who will license the software and charge you their own monthly fees.
- Generally VPN services support multiple protocols.
	- For example SurfShark supports OpenVPN, IKEv2, L2TP/IPSec, SSTP, Wireguard and PPTP as options.

## SSH vs VPN tunnel
- The main difference between an SSH and a VPN is that an **SSH works on an application level**, while a *VPN protects all of your internet data, it works on **transport** layer*. 
	- *SSH encrypts applications rather than the whole traffic coming from your device*.
	- That means you will have to configure each application separately for the SSH tunnel.
	- A VPN, on the other hand, will automatically assure that all your traffic is encrypted so you don’t need to set up encryption for specific apps.

> [!important]- VPN runs on the transport layer while SSH runs on the application layer of a network.
- SSH only works over TCP whereas VPN works over both TCP and UDP.

- *With VPN, your computer becomes part of another network*. 
	- This is done by **creating a virtual network adapter on the system**.
	- With ssh, you just connect to another host, but you don't become part of the server's network.

> [!caution]- How to use SSH tunnelling as a poor man's VPN?
> - We use dynamic port forwarding to use SSH as a tunnel. `ssh -D 9002 user@server_ip`. Here the port need not be 9002, it can be any free port on your system.
> - If you think about it it makes sense you are forwarding all the requests to the other system so any request on port 443 of your system will go to port 443 of other system.
> - *Dynamic port forwarding turns SSH client into a SOCKS proxy server*.
>
> Explore options: `ssh -D 1723 -f -C -q -N user@server.com` -> [Difference between SSH Tunnel / Proxy and VPN in terms of security - Information Security Stack Exchange](https://security.stackexchange.com/questions/200248/difference-between-ssh-tunnel-proxy-and-vpn-in-terms-of-security)

- With dynamic port forwarding you can use SSH tunnel as a VPN but it is for more tech savvy people.
- Also the main difference of using a VPN and using a SSH tunnel using dynamic port forwarding is that this kind of SSH tunnel is *only of a specific application* which connects to the socks proxy (like Firefox) to use it whereas *VPN is for all the applications*.
	- [Use SSH as a SUPER SNEAKY VPN! - YouTube](https://www.youtube.com/watch?v=_Wcxh4-Vt6k)

## Difference between VPN and Proxy
- Just like SSH a proxy works with a single app or site, a VPN secures your network traffic
- A proxy is only suitable for internet browsing and changing your IP.
- With VPN our computer becomes a part of another network.
- *Dynamic port forwarding using SSH is a kind of proxy (SOCKS)*.
- Proxy does not encrypt data whereas VPN does. 
	- But in general if you are using a SOCKS or any other proxy it would support HTTPS which means your data is encrypted while browsing on the web.

## References
- [How VPN works? - YouTube](https://www.youtube.com/watch?v=IzGEfBm0XNY)
- [What is VPN - Installing and Configuring Free OpenVPN Server - Why you need it - ENGLISH VERSION - YouTube](https://www.youtube.com/watch?v=WhTGWYYtUMg) - **MUST WATCH**
	- This *deploys the OpenVPN access server and not the OpenVPN Community Edition* hence the limitation of only 2 users for free license. 
	- If you deploy OpenVPN CE there is no need of licensing and you can have unlimited number of users.
	- But deploying OpenVPN CE is a lot of work and you would also be missing out on the GUI provided by OpenVPN access server.
- [How to set up OpenVPN on Windows? - YouTube](https://www.youtube.com/watch?v=TTm3G_ot3W8) - *How to use OpenVPN with Surfshark*.
	- Basically you download the configuration file (`.ovpn`) and get the credentials from one of Surfshark's VPN server for the OpenVPN client.
- [What is OpenVPN? Explanation of How it Works & When to Use It in 2022 (allthingssecured.com)](https://www.allthingssecured.com/vpn/faq/what-is-openvpn/)
- [How to Make Your Own VPN (And Why You Would Want to) - YouTube](https://www.youtube.com/watch?v=gxpX_mubz2A)
	- For installing OpenVPN from scratch you can get bash scripts online which should do all of the work.
		- [angristan/openvpn-install: Set up your own OpenVPN server on Debian, Ubuntu, Fedora, CentOS or Arch Linux. (github.com)](https://github.com/angristan/openvpn-install)

