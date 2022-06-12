---
created: 2022-05-25 21:24
updated: 2022-05-27 19:35
---
---
**Links**: [[300 home]]

---
## Features of BaaS
- Backend-as-a-Service (BaaS) is a cloud service model in which developers outsource all the behind-the-scenes aspects of a web or mobile application so that they **only have to write and maintain the frontend**. 
- Using a BaaS, you will outsource the responsibilities of running and maintaining servers to a third party and focus on the frontend or client-side development.

- BaaS vendors provide pre-written software for activities that take place on servers, such as 
	- User authentication 
	- Database management
	- Push notifications (for mobile apps)

- Business Advantages: 
	- In particular, for *small to medium size projects*, you will have *substantial benefits* using a BaaS platform.
	- Reduced time to market

> [!question]- When to use BaaS?
> - Making an *MVP* – Minimum Viable Product
> - *Stand-alone apps* or applications that require a small number of integrations
> - Enterprise apps that are not mission-critical

- **IaaS (AWS, Azure etc) vs PaaS (Heroku/[[../100 study-notes/101 AWS SAA/ElasticBeanstalk|ElasticBeanStalk]]) vs BaaS (Firebase)**:
	- IAAS = Data Center + Servers + Storage +Networking
	- PAAS = IAAS + Deploy + Manage + Scale
	- BAAS = PAAS + Features to Build Backend
	- ![[attachments/Pasted image 20220526210031.png]]

- *Most BaaS providers use NoSQL databases on their technology stacks due to scaling flexibility*, but there is a growing trend to use SQL databases like Postgres.

### BaaS vs FaaS (Function as a Service)
- *BaaS* are the services that are meant for *managing and handling backend functionality*. 
	- While *FaaS* is the applications that are being used to *manage and implement microservices* in a more effective way.
- *FaaS* is actually an *event-based architecture* which is helping to execute events. It comes with a higher *simplicity level* and is *auto-scalable*. 
	- While on the other hand, *BaaS* is the *services that are running in the background* to manage the entire backend services such as push notifications, geolocation, user authentication, email verification, database optimisation, etc.

### Some BaaS
- Appwrite 
- Supabase

## References
- [What is BaaS? Backend as a Service explained (back4app.com)](https://blog.back4app.com/backend-as-a-service-baas/)