---
created: 2023-03-25 14:59
updated: 2023-03-26 09:58
---
---
**Links**: [[117 Full Stack Index]]

---
## Session based authentication
- Whenever we log in the backend gives a **session id** which is saved as a cookie.
	- This session id is used find out know which user is making request.
- This cookie is then sent to the backend with every subsequent request.
	- ![[attachments/Pasted image 20230325150908.png]]

> [!note]- We **store the session on the server**.

## JWT based authentication
- Whenever we log in the backend sends a **JWT**.
- We then send this token in the **header** of each subsequent request.
	- ![[attachments/Pasted image 20230325151227.png]]
- The header has a key of `authorization` and the value is `Bearer access_token`
	- ![[attachments/Pasted image 20230325153029.png]]
- A JWT token should be stateless, meaning that **we should store everything you need in the payload** and skip performing a DB query on every request.
	- But we should **only store non-sensitive data** since the contents of the body can be seen by anyone.
	- The body can contain information like *name* of the user, *id*, its *roles*, etc.

> [!note]- We DONT store the JWT on the server.

- With session based authentication, authentication state (session) is managed on the server whereas with JWT based authentication authentication state (tokens) is managed on the client.

### Disadvantages of Session based authentication
- The main issue with sessions is that the session is stored in a server's memory.
- So if the server crashes we also lose the session data and the user will have to log in again.
- In addition to that we need to keep redirecting the user to the same machine storing the session since the new machine won't have user session data and will require the user to log in again.
- We can mitigate few of the problems by storing session in a key value store like Redis but we have to store the session somewhere.
- If there are several micro-services involved in an API call and all of them need to authenticate the user then all of them will need to access Redis. 
- With JWT we can just pass the micro-service to each micro-service.

### What is a JWT
- JWT consists of 3 parts
	- *Header*: gives information about the hashing algorithm
	- *Payload*:  It contains the claims. Claims are statements about an entity (typically, the user) and additional data.
		- Registered claims: `iss` (issue date), `exp` (token expiry date)
		- Public claims: user data like id, username, role etc.
	- *Signature*
- The **contents of the JWT are signed NOT encrypted**.
	- Since they are signed and we don't have the secret key *if we change any of the information* (like username in body) then *JWT will be invalid*.
- For *creating and verifying* a JWT we need a **secret key** (signing key).
- In most frameworks (like express and FastAPI) we can create *middlewares* to handle checking of JWT.
	- A middleware is a function that *runs before every request*.

### Refresh Tokens
 - We add an **expiry date** to a token to ensure that even if the access token is stolen it expires after some time.
 - But the problem is that user will have to login again if the access token expires.
	 - This might not be a very good UX.
	 - We can *use refresh tokens to automatically refresh the access tokens*.
- Just like access tokens refresh tokens are nothing but JWTs.
	- The body of the refresh token can be pretty simple (like just the name of the user).

> [!note]- *Refresh tokens* are **stored in the database** and should be **deleted when the user logs out**.
> Refresh tokens should expire to restrict indefinite access.

- **When refresh tokens expire the user has to login again**.
	- So by **invalidating the refresh token** (deleting from database), the server can prevent the user from obtaining new access tokens, **effectively logging them out of the system**.
 
> [!caution]- Drawback of JWT: **Immediate session invalidation is NOT possible on logouts** since we are not storing access tokens to invalidate.
> The only way to immediately invalidate the access token is *changing the secret key* but this will log out all the users.

- If we want to invalidate JWTs immediately then we would need to store it in some database (redis).
- For banking applications *session based authentication makes sense* since we can immediately revoke access.
- *Every time we login a new access and refresh token is created and refresh token is saved in the database*.
	- A **new session** is created.
	- Good applications give us an option for *invalidating any session we want by expiring that session's refresh token*.
- The choice of using a refresh token depends on the type of application.
	- If you are building a *banking application* then you don't want to automatically refresh the access tokens. 
		- It would be better to just have the *access token with a small expiry* to force the user to log in every time the token expires.
	- But if we are building an application like instagram which requires user retention (continuous login prompts can drive the user away) then we use refresh tokens.
- For automatically generating the new refresh token we can use `axios` *interceptors*.
	- It is a function which will run before each `axios` request.
	- In the function we can check if our access token has expired.
	- If our access token has expired we can make a call to the refresh token endpoint with our refresh token and *renew the access token and store it in memory* (react useState hook).
	- To ensure that this function is not being run when a login request is made we can create a *new axios instance* (one for login requests and other for all other requests).

### Security
> [!important]- How to protect your account if access and refresh token is compromised?
> - A *log out will force the application to delete the refresh token* and *once the access token expires* (hence it is very important to set and expiry for the access token) the hacker won't have access to the account.
> - Log out won't be possible if you are not already logged in (like travelling and you don't have access to your work laptop) so some applications provide the option of revoking the refresh token in the UI.

> [!caution]- NEVER use access tokens with NO expiry.
> This means that once the access token is stolen the *hacker can always access the user data*.

> [!important]- *Access token* should be stored in **application memory** (react useState hooks) and *refresh token* should be stored in **cookies** using `httpOnly` flag.
> - We *should NOT store access tokens in or local storage* (vulnerable to *XSS*, local storage is accessible from JS). 
> - We can store access tokens in `httpOnly` cookies but a more safer approach is to just use application memory.
> - **An `httpOnly` cookie is NOT accessible with JS**.
> - *Refresh tokens should NOT issue new refresh tokens* since it grants indefinite access if refresh tokens fall into the wrong hands.
> ---
> We set the cookie from the backend as a response
> ![[attachments/Pasted image 20230325195340.png]]
> The client never handles the cookie directly. It just sends it when refresh endpoint.
> Similarly when logout is clicked we can remove the cookie using backend.

- If we want to build a *very secure application* then both refresh token and access token should be **stored in application memory** (react useState hook)
	- One downside of this approach is that if the *user closes the tab* then he/she will have to *login again*.
- If we *change the secret key of our application then all the users will be logged out*.
	- Since all access and refresh tokens were invalidated.

### Login flow
- When you do log in, send 2 tokens (Access token, Refresh token) in response to the client.
- The access token will have less expiry time and Refresh will have long expiry time.
- The client (Front end) will store the access token in memory and refresh token in an httponly cookie.
- The client will use an access token for calling APIs. 
- But when it expires, you call auth server API to get the new token (refresh token is automatically added to http request since it's stored in cookies).
- Your auth server will have an API exposed which will accept refresh token and checks for its validity and return a new access token.
- Once the refresh token is expired, the User will be logged out.


## References
- [MERN Stack Authentication with JWT Access, Refresh Tokens, Cookies - YouTube](https://www.youtube.com/watch?v=4TtAGhr61VI)