---
created: 2022-04-19 16:22
updated: 2022-05-14 11:30
---
---
**Links**: [[101 AWS SAA Index]]

---
## NACLs
- It stands for *Network Access Control List*.
- NACLs operate at the **subnet level** whereas SGs operate at the instance level.
	- ![[attachments/Pasted image 20220430085829.png]]

- **NACLs** are **stateless** and **Security Groups** are **stateful**.
    - This means that if the request makes it through the inbound rules of the Security Group (i.e. makes it to the EC2 instance) then the **outbound will be automatically accepted at the Security Group level**. This is because Security Group is stateful whatever is accepted in can also go out.
    - **Since NACLs is stateless the outbound rules will be evaluated** when traffic is going out.

- Example diagram for incoming and outgoing requests
	- ![[attachments/Pasted image 20220430085302.png]]
	- ![[attachments/Pasted image 20220430085312.png]]
    
- So NACLs are *like firewalls* which *control traffic* *to and from at the subnet level*
- We have **one NACL per subnet** and **new subnets** are assigned the **default NACL**.

- NACLs are *great for **blocking** the IP address at the subnet level*.
- *First the NACL is hit* then the Security Group

> [!caution] Another main difference between SGs and NACLs is that SGs *only have allow rules* whereas NACLs have both allow and deny rules.

### NACL Rules
- **Rules** have a **number** (1-32766), **higher precedence with a lower number**
- **First rule match will drive the decision**. Example: if you define #100 ALLOW 10.0.0.10/32 and #200 DENY 10.0.0.10/32, the IP address will be **allowed** because 100 has a higher precedence over 200.
- The **last rule** is an **asterisk (\*)** and **denies** a request **in case of no rule match**
	- ![[attachments/Pasted image 20220430090108.png]]
- AWS recommends *adding rules by increment of 100*

### Newly created NACLs
- **Newly created NACLs** will **deny everything by default**. That is 
	- There is a default *outbound rule denying all* traffic
	- There is a default *inbound rule denying all* traffic

### Default NACLs
- Default NACLs **will accept everything inbound/outbound** with the subnets it's associated with
- If the Default NACL would not have allowed everything then we would have to do some debugging when getting started with AWS.
- It is recommended *not to modify the default NACL and instead create custom NACLs*.

### Ephemeral ports
-   For any two endpoints to establish connection, they must use ports. **Clients connect to a defined port**, and **expect a response on an ephemeral port.**
	- When a client connects to a server, a random port from the ephemeral port range (1024-65535) becomes the client's source port.
	- ![[attachments/Pasted image 20220430090243.png]]

- Different Operating Systems use *different port ranges for ephemeral ports*, examples:
    - IANA & MS Windows → 49152 - 65535
    - Many Linux Kernels → 32768 - 60999

- So if a request comes into a web server in your VPC from a Windows 10 client on the Internet, your network ACL must have an outbound rule to enable traffic destined for ports 49152 - 65535. 