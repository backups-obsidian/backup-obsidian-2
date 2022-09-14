---
created: 2022-09-13 16:25
updated: 2022-09-13 23:43
---
---
**Links**: [[111 KodeCloud Index]]

---
## Webservers
- Flask, Django, Express (NodeJS), Spring (Java) are *web frameworks*. 
	- They help us easily develop web applications.

> [!question]- How do we serve our applications so that it runs and listens on a port to respond back to the users?
> - In some frameworks (like Flask) it provides a way to run the application using the internal web server built into it.
> ![[attachments/Pasted image 20220913232815.png]]
> - But this method is *not recommended for hosting the application in a production environment*.
> - This is the job of a web server.

- How to deploy flask to production
	- We can use Gunicorn, uWSGI, etc.
	- These are production grade web servers.

- **A web framework helps us develop application code but a web server hosts this application**.
- A web server can host multiple applications at the same time.
- *NGINX and Apache Web Server* servers are good example of servers that serve *static content* whereas web servers such as *gunicorn, apache tomcat* are good examples of *dynamic web servers*.
	- ![[attachments/Pasted image 20220913162905.png]]

> [!important]- Static web servers are usually referred to as web servers and *dynamic web servers are referred to as application servers*.

- Using gunicorn for flask
	- `gunicorn app:app`
	- `gunicorn app:app -w 2`: getting 2 workers

- For nodejs (Express) it is advised to use *pm2, supervisord, forever* instead of just running nodejs application.
	- PM2 is a **production process manager for Node.js applications** with a *built-in load balancer*. 
	- It allows you to *keep applications alive forever*, to reload them without downtime and to facilitate common system admin tasks.
	- `pm2 start app.js`
	- `pm2 start app.js -i 4` if you want more instances