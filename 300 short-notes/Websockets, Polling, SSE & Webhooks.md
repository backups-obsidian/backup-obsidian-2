---
created: 2022-05-09 19:06
updated: 2022-05-13 15:06
---
---
**Links**: [[300 home]]
**Recommended Reads**: [[HTTP Deep Dive]]
**Description**: Different ways of achieving realtime communication

---
## Websockets
- Websockets are created using `ws://somewebsite.com` or `wss://somewebsite.com`. wss is for websockets with TLS.

### Features
- Websockets are **stateful** protocol whereas *HTTP is a stateless* protocol.
- HTTP is simplex (either the client sends a request or server sends a response but not at the same time) whereas websockets are **full duplex** (both the client and server can send messages to each other at the same time).
- Websockets are **not horizontally scalable** whereas HTTP is. This is because HTTP is a stateless protocol (sends whatever is needed by the server in every request) and websocket is a stateful protocol. 
- When we refresh the page the websocket connection is closed.

> [!caution] One major con with websockets are that caching, scaling and proxying (specially layer 7) becomes very difficult.

### Handshake
- Websockets are *upgraded [[HTTP Deep Dive#HTTP 1 1|HTTP 1.1]] Get requests*. 
	- ![[attachments/Pasted image 20220511153923.png]]
	- ![[attachments/Pasted image 20220511154325.png]]
- So for establishing a websocket connection you first establish a HTTP 1.1 connection. This HTTP 1.1 connection is then upgraded to a websocket connection by the webserver.
- Uses the same TCP connection that was used for establishing HTTP 1.1.
- Status code of **101** for *switching the protocols*.
- Websockets will never work with [[HTTP Deep Dive#HTTP 1 0|HTTP 1.0]] since there were no persistent connections in HTTP 1.0. 

### Use cases
- Multiplayer gaming
- Chatting applications
- *Dynamic UI*: we can dynamically refresh the UI without reloading the page. This is particularly useful in trading platforms. You see the prices getting update without refreshing the page.
- Showing client progress/logging
- **real time bidirectional communication**.

### References 
- [How Do Websockets Work? - Kevin Sookocheff](https://sookocheff.com/post/networking/how-do-websockets-work/)

## HTTP Polling
- For receiving *regular data updates*.

### Short polling 
- Short polling is a technique where the *client repeatedly sends requests* to the server until it responds with an update.
- As an example—all modern web browsers offer support for XMLHttpRequest, one of the original methods of polling servers.
- *Not ideal for efficient real-time communication as short polling is intensive* since there can be *empty or repeated* responses.
	- ![[attachments/Pasted image 20220512153900.png]]
- It is almost **always a bad idea**.

### Long polling
- When long polling, the client polls the server, and that *connection remains open until the server has any data*. 
- Since the connection is *open for a long time it is known as long polling*.
- When either there is a response from the server or a timeout, then the client closes the older connection and immediately opens another connection with the server.
	- ![[attachments/Pasted image 20220512153932.png]]
- Long polling can seem intensive on the server side, as it requires continuous resources to hold a connection open, but it *uses much less than repeatedly sending polling requests*.

> [!note]- Long polling is much better than short polling. 
> Long polling is also used by [[../100 study-notes/101 AWS SAA/SQS|SQS]] which is used to reduce costs and avoid empty responses.

> [!note]- websockets vs long polling
> - Use websockets when you need low latency

## HTTP Streaming
- For a typical HTTP request the server response is of fixed length. If the *response is of unlimited length then it is known as HTTP streaming*. 
- Now since the response is indefinite the server can keep on sending data indefinitely.
- This can be done in 2 ways:
	- Setting **Transfer-Encoding: Chunked**: Common for non browser clients, like 2 backend servers communicating with each other.
	- Using *SSE: browser based clients*. They can use the standardised eventsource API.

### Server Sent Events (SSE)
- Server can send data to the client over a **long lived TCP connection** but *client cannot send data to the server*.
- SSE use cases:
	- Trading UIs
	- Live feed
	- Sport scores
	- Showing client progress (like a progress bar)

- All the above use cases can be covered with websockets but it will be a waste of resources since client won't be sending any messages. But *websockets are easier to implement* and in the future if the client needs to send any data then you will have to upgrade from SSE to websockets.

- *Used by Twitter* to send users new tweets while they are browsing.
- The client makes an HTTP request, and the server trickles out a *response of indefinite length* (it’s like polling infinitely) 
	- ![[attachments/Pasted image 20220512155929.png]]
	- `Content-Type` headers in request and response must be `text/event-stream`

> [!caution]- Maximum number of open connections is limited to 6 or 8 over HTTP/1.1 (based on the browser version). If you **use HTTP/2**, there won’t be an issue because one single TCP connection is enough for all requests (thanks to multiplexed support in HTTP/2).
> ![[attachments/Pasted image 20220512161449.png]]

#### References
- [Server-Sent Events Crash Course - YouTube](https://www.youtube.com/watch?v=4HlNv1qpZFY)

## Webhooks
- Webhook communication is achieved by sending an HTTP request from a source application to a destination application. When an event takes place in the source application, an HTTP request which might contain data relating to the event is triggered. This HTTP request is sent to the destination application's endpoint (often referred to as the webhook URL.)
	- ![[attachments/Pasted image 20220513105003.png]]
	- ![[attachments/Pasted image 20220513105710.png]]
	- WebHook provider is responsible for monitoring the event, once the event is triggered, the WebHook provider will send the POST HTTP request to the specified URL.

- GitHub webhook example
	- ![[attachments/Pasted image 20220513113910.png]]
	- You specify your own URL on which you want to receive the requests.

> [!important] **Webhooks are mostly used between two servers to pass information**, while *WebSockets are used primarily for server-to-client (mostly web browsers)* communication.

- Webhooks are also known as **reverse APIs** since in normal APIs the requests originate from the consumer whereas in webhooks the requests generate from the server.
- Lightweight solution for enabling *real-time notifications and data updates*.

> [!info] Webhooks are *far more efficient than pooling* for getting event driven real time updates.

> Webhooks is like don't call us we will call you 

> [!caution]- The URL provided by the client for webhooks must be *publicly accessible* and is managed by the client (event consumer)
> - Webhooks forces the event consumer to establish a publicly accessible HTTP endpoint to receive events. 
> - It comes with other concerns like securing it with certificates, preventing DDoS attacks, etc. These could be a burden in the long run in terms of maintenance.
> - Moreover, Webhooks *can’t be used to push event notifications to end-user consumers such as mobile and Single Page Applications (SPA) as they are not in possession of an HTTP endpoint*.
> - WebSockets are far more efficient than WebHooks as they remove the HTTP header overhead by operating on the TCP layer. They can be used for push notifications in SPA or mobiles.
> - Despite the above drawbacks, Webhooks can still be *ideal for implementing a server-to-server event notification* mechanism.

### Difference between APIs and webhooks
- API is a pull system whereas webhook is a *push system*.
- A webhook can be thought of as a type of API that is *driven by events* rather than requests.
- API over webhook
	- ![[attachments/Pasted image 20220513113210.png]]

### Simple webhook example
- We have an application with payment linked to stripe. Whenever a payment is processed stripe sends post request to our webhook url which is then processed by our server.

- Stripe server side code
```python
import requests
import json

# localhost will be replaced by our public url
# we provide the webhook url using the UI while creating a webhook in stripe
webhook_url = 'http://localhost:5000/webhook'
data = 'somejsondata'
requests.post (webhook_url, data-json.dumps(data), headers={' Content-Type': 'application/json'})
```

- Our server side code
```python
from flask import Flask, request, abort
app = Flask(__name__)

@app.route('/webhook' methods=['POST'])
def webhook():
	if request.method == 'POST':
		print(request.json)
		return ('success', 200)
	else:
		abort(400)

if __name__ == '__main__':
	app.run()
```

- Now if our server side code is running (flask server is running) then when we run the stripe side code (simulating an event like payment) then we our server will receive data.

#### References
- [How to Send Webhooks with Python. - YouTube](https://www.youtube.com/watch?v=X-_25tzo8Cw) 
- [How to Receive Webhooks with Python - YouTube](https://www.youtube.com/watch?v=HQLRPWi2SeA)