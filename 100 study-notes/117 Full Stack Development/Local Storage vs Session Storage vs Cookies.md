---
created: 2023-03-26 10:17
updated: 2023-03-26 10:18
---
---
**Links**: [[117 Full Stack Index]]

---

## Local Storage vs Session Storage vs Cookies
- Local Storage, Session Storage and cookies allows us to store data as *key value pairs* as **strings** in the browser's memory.
- Local Storage:
	- Persistent in browser memory.
		- The **data persists** even if we close the tab, browser or restart the computer.
		- The only way to delete this data is to explicitly delete it.
	- **Accessible with JavaScript** only.
	- *NOT automatically sent to the server* during an HTTP request.
- Session Storage:
	- **Deleted once the browser tab or window is closed**.
	- **Accessible with JavaScript** only.
	- *NOT automatically sent to the server* during an HTTP request. 
	- Refreshing the web page won't delete the data.
- Cookies:
	- Persistent in browser memory.
		- The **data persists** even if we close the tab, browser or restart the computer.
		- The only way to delete this data is to explicitly delete it.
	- **Inaccessible with JavaScript** (with the `HttpOnly` flag).
	- **Automatically sent to the server** during an HTTP request.
	- A cookie is automatically deleted if it has an expiration date.
- Since *local storage and session storage are accessible via JS* they are *vulnerable to XSS* (cross site scripting attacks).

> [!question]- Why are cookies used to store session or token data?
> - Cookies when used with the `HttpOnly` flag are not accessible with JavaScript and are thus *immune to XSS attacks*.
> - Cookies when used with the `Secure` flag ensure cookies can only be *sent securely through HTTPS*.

- Cookies are vulnerable to CSRF (Cross site request forgery) attacks
	- This can be *mitigated using `SameSite` flag in cookies*.