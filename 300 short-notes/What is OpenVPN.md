---
created: 2022-11-12 09:29
updated: 2022-11-12 19:52
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

## References
- [How VPN works? - YouTube](https://www.youtube.com/watch?v=IzGEfBm0XNY)
- [What is VPN - Installing and Configuring Free OpenVPN Server - Why you need it - ENGLISH VERSION - YouTube](https://www.youtube.com/watch?v=WhTGWYYtUMg) - **MUST WATCH**
	- This *deploys the OpenVPN access server and not the OpenVPN from scratch* hence the limitation of only 2 users for free license. 
	- If you deploy OpenVPN from scratch there is no need of licensing and you can have unlimited number of users.
- [How to set up OpenVPN on Windows? - YouTube](https://www.youtube.com/watch?v=TTm3G_ot3W8) - *How to use OpenVPN with Surfshark*.
	- Basically you download the configuration file (`.ovpn`) and get the credentials from one of Surfshark's VPN server for the OpenVPN client.
- [What is OpenVPN? Explanation of How it Works & When to Use It in 2022 (allthingssecured.com)](https://www.allthingssecured.com/vpn/faq/what-is-openvpn/)
- [How to Make Your Own VPN (And Why You Would Want to) - YouTube](https://www.youtube.com/watch?v=gxpX_mubz2A)
	- For installing OpenVPN from scratch you can get bash scripts online which should do all of the work.