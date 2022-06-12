---
created: 2022-05-09 19:06
updated: 2022-05-10 20:13
---
---
**Links**: [[300 home]]

---
## History of HTTP
![[attachments/Pasted image 20220509192559.png]]

## HTTP 0.9
- Initial version of HTTP — a simple client-server, request-response, telenet-friendly protocol
- *Request nature: single-line* (method + path for requested document)
- Methods supported: `GET` only
- Response type: *hypertext only*
- Connection nature: terminated immediately after the response
- No HTTP headers (cannot transfer other content type files), No status/error codes, No URLs, No versioning

## HTTP 1.0
- *Provided header fields* including rich metadata about both request and response (*HTTP version number, status code, content type*)
- Response: *not limited to hypertext* (`Content-Type` header provided ability to transmit files other than plain HTML files — e.g. scripts, stylesheets, media)
- Methods supported: `GET` , `HEAD` , `POST`
- Connection nature: terminated immediately after the response i.e. establishing a **new TCP connection with each request**.
	- ![[attachments/Pasted image 20220509200607.png]]
- Establishing a *new connection for each request* — *major problem* in both HTTP/0.9 and HTTP/1.0. This meant these protocols were *very slow*.
- It was realised quickly by the devs and *within a year HTTP 1.1 was released*.

## HTTP 1.1
- Internet landscape was constantly changing with websites becoming more dynamic and heavy. HTTP 1.0 was very slow so a new and faster protocol was needed.
- Extra methods were added like `PUT`, `DELETE`
- Features like **CORS**, **Keep-alive**, **Caching** were introduced in this update.
- So in HTTP 1.1 we had **Persistent TCP Connection** using keep alive header.

### Keep alive
- The `Keep-Alive` *header* was first introduced in HTTP 1.1 
	- HTTP/1.1 changed the semantics of the HTTP protocol to use connection **keepalive by default**. Meaning, unless told otherwise (via `Connection: close` header), the server should keep the connection open by default.
	- However, this same functionality was also backported to HTTP/1.0 and enabled via the `Connection: Keep-Alive` header. Hence, if you are using HTTP/1.1, technically you don’t need the `Connection: Keep-Alive` header, but many clients choose to provide it nonetheless.

- Using Keep-alive option meant that the server won't close the TCP connection. 
- This enabled *re-using of the same TCP connection for multiple HTTP request*. 
	- ![[attachments/Pasted image 20220509212942.png]]

- Client, server, or any intermediary can provide information for `Keep-Alive` header independently. Also, a host can add `timeout` and `max` parameters in order to set a timeout or limit maximum request count per connection.

- Example
	- ![[attachments/Pasted image 20220510145407.png]]

### Flaws in HTTP 1.1
- HOL - **Head of Line Blocking**
	- Suppose a request is send for index.html file of a website.
	- A single TCP connection is created. 	
	- *TCP Connection gets blocked till the response is received*.
		- ![[attachments/Pasted image 20220510150129.png]]
	- Internally the index.html also requires some CSS and JS files.
	- Now since we have a single TCP connection it will wait for the response of index.html files before requesting the CSS and JS files.
	- One way of making it a bit better is by *establishing multiple TCP connections (6)*. But this is working around the problem instead of solving it. What if you have more than 6 things then we are back to the initial problem.
		- ![[attachments/Pasted image 20220510154008.png]]
		- ![[attachments/Pasted image 20220510200345.png]]
	- Analogy for HOL
		- A simple analogy is a platform on a railways station. If the platform is occupied by a train, all the trains that are behind are blocked until this train departs. Obviously, blocked trains can occupy other free platforms, but what if all other platforms are also occupied. This is what actually browsers do to speed up page loads with HTTP/1. They open multiple TCP connections and route pending requests through those TCP connections, but there is a limit to the number of TCP connections that can be opened and currently, this is 6 for chrome and Mozilla firefox.

- Repetition of Header Data
	- Header information is repeated with each request.

## HTTP 2
- Header data is separate from request data.
- *Compress HTTP Headers* using **HPACK**
- Stream prioritisation.
- Allows *Server Push*:
	- Push frames enables us to send mandatory resources in advance along with an HTTP response.
	- Server Push can be abused when configured incorrectly
- *HTTPS* is a mandatory requirement
- HTTP 2 is built upon HTTP 1.1 so even though the client doesn't support HTTP 2 it will served using HTTP 1.1.
- HTTP/2 encodes request and response messages into **binary**, instead of transmitting the normal plain-text messages you would see with HTTP/1.1.
- HOL Blocking problem in HTTP 1.1 is solved by **multiplexing**.

### Working
- HTTP/2 breaks down the HTTP protocol communication into an exchange of **binary-encoded frames**, which are then mapped to *messages* that belong to a particular *stream*, and all of which are multiplexed within a *single TCP connection*.

> [!important]+ **Frames → Messages → Streams → A single TCP connection**
> - The *frame* is the *smallest unit of communication* that carries a specific type of data — e.g., HTTP headers, message payload, and so on.
> -  Each *message* is a *logical HTTP message*, such as a request, or response, which consists of *one or more frames*.
> - Each *stream* has a *unique identifier* and optional priority information that is used to carry bidirectional messages. 
> - A single TCP connection can have *hundreds of streams*.

> [!note]- The ability to break down an HTTP message into independent frames, interleave them, and then reassemble them on the other end is the single most important enhancement of HTTP/2.
> ![[attachments/Pasted image 20220510194815.png]] 
> ---
> The above figure has three parallel streams(stream 1, stream 3 and stream 5) in a single connection. While the client is transmitting a DATA frame to the server(Over stream 5) the server, on the other hand, is transmitting an interleaved sequence of frames to the client over stream 1 and stream 3.

### Multiplexing
- HTTP/1.1 loads resources one after the other, so if one resource cannot be loaded, it *blocks all the other resources behind it*. 
- In contrast, *HTTP/2* is able to use a single TCP connection to send *multiple streams of data at once* so that no one resource blocks any other resource.
	- ![[attachments/Pasted image 20220510150259.png]]
- To achieve this parallel request over a single HTTP connection, **HTTP 2 uses the concept of streams**. 
- That is, each request being sent from the client has a *unique stream id* attached behind the scenes. This helps the client and server identify the calling and receiving endpoints. One can think of *each stream as an independent channel* for communication.
	- ![[attachments/Pasted image 20220510150310.png]]
	- ![[attachments/Pasted image 20220510154249.png]]

### Flaw - TCP HOL Blocking
- HTTP 2.0 is great until there’s a hiccup in the network connection, like network congestion or moving from one cell to another on the mobile network and when a packet gets lost. 
- *TCP guarantees that the order in which packets are sent is the order in which received by the app* — so if you miss one, everything has to stop until that particular packet gets retransmitted. 

> [!important] If you multiplex multiple requests onto a single TCP connection, then all those requests have to stop and wait even though the lost packet might affect only one of them.

- This *TCP head of line blocking problem* is inherent to TCP and *UDP fixes it by allowing the application to control the retransmission of packets*. 
- Ideally, a missing packet should have affected only the stream from which packet was lost, but instead, *it blocks all other streams*. 

> [!caution] This makes HTTP/2 highly unreliable and **worse performant** in **poor network conditions**.

## HTTP 3
- Perhaps the most obvious difference between HTTP/3 and the older versions is that HTTP/3 is fully based on **QUIC**, which utilises **UDP**.
- HTTP/3 also includes TLS 1.3 encryption.
	- ![[attachments/Pasted image 20220510201255.png]]
- From TCP + TLS to UDP + QUIC.

## References
- [HTTP/1 to HTTP/2 to HTTP/3. It’s not about being the best, it’s… | by Sandeep Verma | Medium](https://medium.com/@sandeep4.verma/http-1-to-http-2-to-http-3-647e73df67a8)