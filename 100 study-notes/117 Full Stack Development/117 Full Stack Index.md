---
created: 2023-03-25 14:58
updated: 2023-03-26 11:53
---
---
**Links**: [[../100 home | 100 Home]]

---
[[../116 CSS/116 CSS Index | CSS]]
[[../115 React/115 React Index | React]]
[[Local Storage vs Session Storage vs Cookies]]
[[Full Stack - Authentication]]

- Advantages of nextjs over reactjs
	- In built routing
	- Automatic code splitting
	- API routes using functions
	- Image optimization
	- SSR (server side rendering) - SSG (server side generation) - ISR (incremental static regeneration)
		- ![[attachments/Pasted image 20230326115229.png]]
		- React is CSR (client side rendering)
		- SSR is best when we have dynamic data.
		- SSG is best when we have static data like blogs.
- In reactjs when the users visit our site they receive an HTML document, CSS and a large JS.
	- This JS is executed to load the website.
	- ![[attachments/Pasted image 20230326114441.png]]
- In SPA (single page applications) there are no page reloads.
	- Poor SEO
	- React is an example of SPA.