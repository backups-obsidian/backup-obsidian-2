---
created: 2022-04-22 22:17
updated: 2022-05-17 21:32
---
---
**Links**: [[300 home]]
**Recommended Reads**: [[HTTP Deep Dive]]

---
## Why gRPC
- Today's trend is to build micro services. For these services to communicate with each other we need APIs. 
- Standard approach is to build REST APIs. But it comes with a lot of problems. Like:
	- You need to think about the endpoint.
	- You need to think about how to invoke the API and how to handle errors.
	- You need to have a retry mechanism in case of failures.
	- You need to think about efficiency, latency, interoperability etc,
	- You need to think about logging, authentication etc.
	- Now if there are multiple micro services all these need be done for all the micro services. 

- gRPC aims to solve all the above problems by abstracting it from us and standardising the communication between services.

## What is RPC
- An RPC is a Remote Procedure Call.
- It is designed to make a network call look just like a function call. It does this by abstracting all the complexities like serialisation, deserialisation and transport.
- In your CLIENT code, it *looks like you're just calling a function directly on the SERVER*.
	- ![[attachments/Pasted image 20220422222840.png]]

- RPC is not a new concept but in gRPC it has been implemented cleanly and solves a lot of problems.
- **There are different RPC implementations but the most famous is gRPC**. 

### Stubs
- In RPC data is *serialised/deserialised*. **Stub is responsible for serialisation and deserialisation**. 
- In the example given below Golang's struct is first serialised to be sent over network and then deserialised to be converted to Java's class. All this is done by stub.
	- ![[attachments/Pasted image 20220513203144.png]]
	- So in short a stub converts *methods*, *request types*, and *response types* into a form used by the RPC system.

> [!note] Stub is the main reason why RPCs appear to be like local function calls.

- gRPC uses *protobuf format to define stubs*. The extension of the file is `.proto`
- `.proto` file is also known as *stub interface definition*.
- Stub generator generates the code for us.

### Communication in RPC
- RPC can use any layer 3 protocol *TCP or UDP*.
- RPC can *also happen over HTTP*.
- For example gRPC uses HTTP 2 for its transport layer.
- You can also write your own RPC implementation and use websockets for communication.

## What is serialisation and deserialisation
- Data serialisation is the process of **converting an object into a stream of bytes** to more easily *save* or *transmit* it.
	- ![[attachments/Pasted image 20220513195028.png]]
- Serialisation and deserialisation work together to transform/recreate data objects to/from a *portable format*.
- The verbs Marshal and Unmarshal are synonymous with serialise and deserialise
- Serialising takes complex data and lists it in a sequential manner. This is what pickle (python library for writing to files) does under the hood.

### Serialisation formats
- Some *serialisation formats* are:
	- *JSON*: serialised to **plain text**. In JavaScript, for example, you can serialise an object to a JSON string by calling the function `JSON.stringify()`.
	- *Protobuf*: serialised to **binary**. 
	- *XML*  

> [!tip]+ JSON vs Protobuf
> - Like JSON **Protobuf is also a message format**.
> - *JSON* is great when you have *small volume of message exchange* where as *protobuf* is great when you have a *high volume of message exchange*.
> - *Protobuf* is something you would use if *performance really matters*.

## What is gRPC
- It is a *free and open source* framework developed by *google*. It is now a part of CNCF (Cloud Native Computing Foundation) like Kubernetes and Prometheus.
- At a high level, it allows you to define REQUEST and RESPONSE for *RPC (Remote Procedure Calls)* and handles all the rest for you.
- On top of it, it's *modern*, *fast and efficient*, build on top of **HTTP/2** (implementation is abstracted from us), *low latency*, supports *streaming*, *language independent*, and makes it super easy to plug in *authentication*, load balancing, *logging and monitoring*.
- It is best for building *micro services*.
- At the core of gRPC, you need to *define the messages and services* using **Protocol Buffers**
- The rest of the gRPC code will be generated for you and you'll have to provide an implementation for it.
- One `.proto` file works for over 12 programming languages (server and client), and allows you to use a framework that scales to millions of RPC per seconds.
	- ![[attachments/Pasted image 20220425223839.png]]

## References
- [Introduction to RPC - Remote Procedure Calls - YouTube](https://www.youtube.com/watch?v=eRndYq8iTio)