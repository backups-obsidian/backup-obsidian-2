---
created: 2022-11-03 19:58
updated: 2022-11-04 16:36
---
---
**Links**: [[../100 home | 100 Home]]

---

- Nginx can act as a:
	- [[Nginx - Web server]]
	- [[Nginx - Reverse Proxy]]
	- [[Nginx - Load Balancer]]


- Nginx acting as a load balancer
	- ![[attachments/Pasted image 20221103200509.png]]
- Perform TLS termination
- Location of nginx configuration files in docker: `/etc/nginx`
- The key value pairs in nginx are known as directives.
	- *Directives must end with semicolon*.
	- ![[attachments/Pasted image 20221103201502.png]]
- The blocks of code in the curly braces is known as context.
	- Within the context we can have directives particular to that context.

- Configuration file: `nginx.conf`
- Serving static content.
	- Since we will be dealing with `http`, we will need an `http` context.
	- **We might not be needing the `events` context but we need to define it for nginx to work**.
	- Inside `http` context we define the `server` context.
	- `root` directive inside the `server` context is the location of a bunch of different files that we would like to serve.
	- This is all we need to do serve the static content.

```nginx.conf
http {
	server {
		listen 8080;
		root /some/location/here;
	}
}

events{}
```

> [!note]- When we change nginx configuration we need to RELOAD it.
> `nginx -s reload`

- We cannot directly serve CSS by just including it in the root folder. 
	- If we do that then it will be served but the `Content-Type` will be `text/plain` where as it should be `text/css`.
	- ![[attachments/Pasted image 20221103203226.png]]

- Inside the `http` context we can define the different types using the `types` context
```nginx.conf
http {
	types {
		text/css css;
		text/html html;
	}

	server { ... }
}
```

- But there are so many types that it would get cumbersome. 
	- Luckily nginx comes `mime.types` file.

- Including `mime.types` in the `nginx.conf` folder
```nginx.conf
http {
	include mime.types;

	server { ... }
}
```

- Since they are in the same folder we can just specify the file name but the absolute path of the file at any location like `include /etc/nginx/mime.types;` will also work.

- `location` context inside the `server` context

```nginx.conf
http {
	server {
		listen 80;
		root /somepath/abc;

		location /fruits {
			root /somepath/abc; 
			# location of fruits folder (/somepath/abc/fruits)
		}

		location /carbs {
			alias /somepath/abc/fruits;
		}
		# now /carbs and /fruits will serve the same thing
	}
}
```

- Location path is automatically appended to root.
- `alias` doesn't append to the end.

> [!note]- `root` appends path, `alias` doesn't.

> [!caution]- In the above examples we were **assuming that we have an `index.html` in the folders specified by `root` or `alias`**.

- By default it looks for `index.html` but we can specify `try_files` and specify a bunch of different directories we want it to specify.

```nginx.conf
http {
	server {
		location /vegetables {
			root /somelocation;
			try_files /vegetables/veggies.html /index.html =404;
			# if we don't get veggies.html, index.html then give a 404
		}
	}
}
```

- Redirects and rewrites.
- Redirect someone when he goes to a particular path

```nginx.conf
http {
	server {
		location /crops {
			return 307 /fruits; # redirect
		}
	}
}
```

> [!note]- In a redirect the URL changes but in a rewrite the URL remains the same but different content is served.

- `rewrite` directive is outside the `location` context

- Load balancer
	- In the **`upstream` (backend servers) context** we specify the backend servers.

```nginx.conf
http {
	upstream backend {
		server 127.0.0.1:1111;
		server 127.0.0.1:2222;
	}
	server {
		listen 80;
		location / {
			proxy_pass http://backendserver/;
		}
	}
}
```

> [!note]- Nginx uses `proxy_pass` directive to forward the request to the upstream/backend server.

- Nginx does proxying by utilising the `proxy_pass` directive.
- A `proxy_pass` directive **moves the incoming request to the replacement destination **. 
	- The replacement destination can be an *IP address*, *domain name*, *upstream name (backend server)*, unix socket.

- Nginx's default behaviour is to close the connection before it initiates a new connection to the backend/upstream.
	- In this process some of the original request information is lost.
- To preserve header information we can use a directive called `proxy_set_header`.
	- The first argument is the header name and second argument is the value which should be populated in that header.
	- Nginx rewrites/redefines the headers before sending them to the backend.

```nginx.conf
http {
	server {
		listen 80;
		# these headers can also be added in the location context
		proxy_set_header Host $host;
		proxy_set_header X-Real-IP $remote_addr;
		proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

		location / {
			# specifying the location of error and access logs.
			access_log /var/log/nginx/reverse-access.log;  
			error_log /var/log/nginx/reverse-error.log;
			proxy_pass http://10.1.1.4:9000/app1;
		}
	}
}
```

> [!important]- Reverse proxy sits in front of the web server.
> - It is possible that nginx is the reverse proxy sitting in front of nginx web server.
> - It is possible to have nginx behave as a webserver on `/` and behave as a reverse proxy on`/something` 

> [!caution]- CDNs are an advanced form of reverse proxy with most of the configuration and maintenance taken care of by a third-party.

## Features
- **Load balancing**: Nginx load balance the client request to multiple upstream servers evenly which improve performance and provide redundancy in case of server failure. This helps to keep the application up all the time to serve client requests and provide better SLA for application.
- **Security**: Nginx server provides security to backend servers that exist in the private network by hiding their identity. The backend servers are unknown to the client that are making requests. it also provides a single point of access to multiple backend servers regardless of the backend network topology.
- **Caching**: Nginx can serve static content like image, videos, etc directly and deliver better performance. It reduces the load on the backend server by serving static content directly instead of forwarding it to the backend server for processing.
- **Logging**: *Nginx provides centralized logging for backed server request and response passing through it and provides a **single place** to audit and log for troubleshooting issues*.
- **TLS/SSL support**: Nginx allows secure communication between client and server using TLS/SSL connection. User data remains secure & encrypted while transferring over the wire using an HTTPS connection.
- **Protocol Support**: Nginx supports HTTP, HTTPS, HTTP/1.1, HTTP/2, gRPC - Hypertext Transport Protocol along with both IP4 & IP6 internet protocol.

## Common options
```
location/ {
	proxy_pass http://127.0.0.1:3000;
	proxy_http_version  1.1;
	proxy_cache_bypass  $http_upgrade;
	
	proxy_set_header Upgrade           $http_upgrade;
	proxy_set_header Connection        "upgrade";
	proxy_set_header Host              $host;
	proxy_set_header X-Real-IP         $remote_addr;
	proxy_set_header X-Forwarded-For   $proxy_add_x_forwarded_for;
	proxy_set_header X-Forwarded-Proto $scheme;
	proxy_set_header X-Forwarded-Host  $host;
	proxy_set_header X-Forwarded-Port  $server_port;
	proxy_set_header X-Test            $request_uri;
}
```

-   `proxy_http_version 1.1` - Defines the HTTP protocol version for proxying, by default it it set to 1.0. For Websockets and `keepalive` connections you need to use the version 1.1.
-   `proxy_cache_bypass $http_upgrade` - Sets conditions under which the response will not be taken from a cache.
-   `Upgrade $http_upgrade` and `Connection "upgrade"` - These header fields are required if your application is using Websockets.
-   `Host $host` - The `$host` variable in the following order of precedence contains: hostname from the request line, or hostname from the `Host` request header field, or the server name matching a request.
-   `X-Real-IP $remote_addr` - Forwards the real visitor remote IP address to the proxied server.
-   `X-Forwarded-For $proxy_add_x_forwarded_for` - A list containing the IP addresses of every server the client has been proxied through.
-   `X-Forwarded-Proto $scheme` - When used inside an HTTPS server block, each HTTP response from the proxied server is rewritten to HTTPS.
-   `X-Forwarded-Host $host` - Defines the original host requested by the client.
-   `X-Forwarded-Port $server_port` - Defines the original port requested by the client.

## Localhost example
```
server {
    # Port
    listen       8080;

    # Set the name of the virtual server
    server_name  _;

    # Proxy pass everything to http://localhost:4206/
    location / {
        proxy_pass http://localhost:4206;
        # Set the proxied host
        proxy_set_header Host localhost;
    }
}
```

## SideCar Proxy


## Try out
- [ ] Set proper access logs format from the nginx video and check if its working or not.
- [ ] Run a frontend container. Just expose port 80. Don't expose port 3000. Now try accessing it. It should work.
- [ ] Now use path like /dashboard in NGINX and see if everything is working fine.
- [ ] Add headers to see if the headers are returned in response.
- [ ] Check access logs when you do the above experiments.
- [ ] Try out side car proxy with strip_path = False

## References
- [The NGINX Crash Course - YouTube](https://www.youtube.com/watch?v=7VAI73roXaY)
- [Configure NGINX as a Reverse Proxy - YouTube](https://www.youtube.com/watch?v=lZVAI3PqgHc)