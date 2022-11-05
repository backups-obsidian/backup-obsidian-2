---
created: 2022-11-03 19:58
updated: 2022-11-05 16:13
---
---
**Links**: [[../100 home | 100 Home]]

---
Nginx can act as a webserver, reverse proxy and a load balancer.

[[Nginx - Basics]]
[[Nginx - Web server]]
[[Nginx - Reverse Proxy]]
[[Nginx - Load Balancer]]

## Try out / Experiment
- [ ] Set proper access logs format from the nginx video and check if its working or not.
- [ ] Run a frontend container. Just expose port 80. Don't expose port 3000. Now try accessing it. It should work.
- [ ] Now use path like /dashboard in NGINX and see if everything is working fine.
- [ ] Add headers to see if the headers are returned in response.
- [ ] Check access logs when you do the above experiments.
- [ ] Try out side car proxy with strip_path = False

## References
- [The NGINX Crash Course - YouTube](https://www.youtube.com/watch?v=7VAI73roXaY)
- [Configure NGINX as a Reverse Proxy - YouTube](https://www.youtube.com/watch?v=lZVAI3PqgHc)
- [The NGINX Handbook – Learn NGINX for Beginners (freecodecamp.org)](https://www.freecodecamp.org/news/the-nginx-handbook/)