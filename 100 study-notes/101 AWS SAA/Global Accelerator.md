---
created: 2022-04-19 19:08
updated: 2022-05-01 21:35
---
---
**Links**: [[101 AWS SAA Index]]

---

- When you go to Global Accelerator it goes to **us-west-2**
- *Global accelerator* uses the concept of **Anycast IP**.
- **Unicast IP**: one server holds one IP address
	- ![[attachments/Pasted image 20220424194957.png]]
- **Anycast IP**: *all servers hold the same IP address* and the client is **routed to the nearest one**
	- ![[attachments/Pasted image 20220424195034.png]]

- Global accelerator *uses the AWS Global network*. This *minimises latency* since over public internet the connection has to go over a lot of hops which can add latency.

> [!question]+ How does Global Accelerator work?
> - In global accelerator **2 anycast IPs** are created. 
> - *Traffic* is directly *sent to the edge location*. 
> - The edge locations send traffic to our application over the *private global AWS network*.
> ---
> ![[attachments/Pasted image 20220424195538.png]]

- It has *DDoS protection* with the help of **AWS Shield**.
- Global health checks for **automatic failover**.
- Intelligent routing to lowest latency.

- It provides static IP addresses that provide a **fixed entry point** to your applications and *eliminate the complexity of managing specific IP addresses for different AWS Regions and Availability Zones*.
- Global Accelerator is a good fit for **non-HTTP use cases**, such as *gaming (UDP)*, *IoT (MQTT),* or Voice over IP.

- It **works with**:
	- *Elastic IP* 
	- *EC2 instances* 
	- *ALB and NLB* either private or public.

> [!tip]+ Whenever you see these keywords like these then go for AWS Global Accelerator.
> - *2 static IP addresses*
> - *AWS Global network* 
> - *Automatic failover* 
> - *routing to the closest region*
> - *TCP/UDP*
> - *latency*
> - *doesn't want to change their DNS service* 

> [!question]- You want to do **blue green deployment** and most of the customers use mobile phones which are prone to DNS caching.
> AWS Global Accelerator *uses endpoint weights* to determine the proportion of traffic that is directed to endpoints in an endpoint group, and traffic dials to control the *percentage of traffic* that is directed to an endpoint group. 
> ---
> While relying on the *DNS service (Route53)* is a great option for blue/green deployments, it *may not fit use-cases that require a fast and controlled transition of the traffic*. Some client devices and internet resolvers *cache DNS answers for long periods*. The downside of this in blue/green deployments is that you *don't know how long it will take before all of your users receive updated IP addresses* when you update a record, change your routing preference or when there is an application failure. 
> With AWS Global Accelerator, you can shift traffic gradually or all at once between the blue and the green environment and vice-versa *without being subject to DNS caching on client devices and internet resolvers*, traffic dials and endpoint weights changes are effective within seconds.
### Difference between CloudFront and Global Accelerator
- Both of them make use of the edge locations
- *Both* services integrate with *AWS Shield* for DDoS protection.
- **CloudFront caches the content** and then delivers it whereas **Global Accelerator is like** a **proxy** to the region your application is in.
- In case of **CloudFront content** is **served from Edge** (in case of cache hit) whereas in **Global Accelerator** it is served from the **region your application is hosted**.
- Global Accelerator is good for HTTP use cases that require static IP addresses.
