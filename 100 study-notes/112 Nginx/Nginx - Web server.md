---
created: 2022-11-03 20:02
updated: 2022-11-05 15:43
---
---
**Links**: [[112 Nginx Index]]

---
## Nginx as a web server
- It is typically used as a **web server for serving static files**.
- It is treated as a web server but the technical term for this is a reverse proxy.
	- Gets the content from other servers.
	- ![[attachments/Pasted image 20221103200329.png]]

### Serving static content.
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

### Location Context
- `location` context is inside the `server` context

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

### Redirects and Rewrites
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