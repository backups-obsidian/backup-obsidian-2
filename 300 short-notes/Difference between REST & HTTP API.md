---
created: 2022-05-26 10:19
updated: 2022-05-30 22:06
---
---
**Links**: [[300 home]]

---
## REST vs HTTP
- *Most developers always associate REST with HTTP and that’s where the confusion arises*.
- HTTP API is also known as a web API

> [!note]+ **Any transfer protocol can be used to create a RESTful API**. 
> - REST is not necessarily tied to HTTP. 
> - Rest is an *architectural style* which may use HTTP, FTP or other communication protocols but is widely used with HTTP.

- *RESTful web services* are just web services that *follow a RESTful architecture*
- **HTTP is a a communication protocol and REST is a conceptual contract**. 
	- REST implies a *series of constraints (6)* about how Server and Client should interact. 
	- HTTP is a communication protocol with a given mechanism for server-client data transfer. 

- An *HTTP API simply only says that the HTTP protocol is used*. A HTTP API doesn't necessarily have to be a REST API if it doesn't follow REST architectural styles.
	- This means that *even SOAP* (Simple Object Access Protocol) can be *regarded as HTTP API* if it uses HTTP for transport

> [!caution]- If the constraints specified by the REST is not met then the API is just an HTTP API and not a REST API.

## What is REST
- REST is about how the client should be asking things from the server and how the server should be answering those requests.
- *Representation of entities* is the central idea in REST
- *Everything is a resource* in REST.
	- Entity in you application ~ Resource. 
	- For example if you are building a library management system then student is a resource, book is a resource.
- How data is stored is not enforced by REST. 
	- Like for a library management system you can store your resources (students, books) in any way you want. You can use SQL or NOSQL or any other way.
	- **Store it however you want but serve it in the representation client is asking for**. 
		- Representation can be in JSON, Text, XML etc
		- ![[attachments/Pasted image 20220530211846.png]]
- REST blends in with HTTP very well because of the HTTP verbs.
	- **Action** is specified by the **verb** and **resource** is specified by the **URL**
	- *URL is used for the identification of the resource* and not the action. For action we have verbs.
- With *HTTP verbs* we can *multiplex operations* on the same URL
	- ![[attachments/Pasted image 20220530212337.png]]

> [!note] The most important point why REST API has become synonymous with HTTP is that it blends really well with the existing HTTP infrastructure (caches, load balancers, etc) and nothing has be reinvented for it to work. 

## Downsides of REST over HTTP
- *Consumption is not very easy*
	- Not as simple as Stubs in RPC we could need an *HTTP client to make request*, get response in say JSON, *convert it to native object* and then consume it
- *Consumption is repetitive*
	- Everyone who consumes/adopts REST is writing the same stuff like serialisation/deserialisation, failures, timeouts, compression etc.
- Some HTTP servers may not support all HTTP verbs
	- It is upto HTTP servers to provide for HTTP verbs
- **HTTP payloads are huge** and are *not suitable for low latency applications*
	- JSON is built for readability
	- For low latency requirements we use *RPC*
- It is not easy to switch protocols like from TCP to UDP
