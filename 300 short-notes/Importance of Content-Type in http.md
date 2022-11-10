---
created: 2022-11-07 15:18
updated: 2022-11-07 15:59
---
---
**Links**: [[300 home]]

---
## Importance of Content-Type in HTTP 
- The **content type status, part of an HTTP header, can tell a browser what type of content**, e.g. a PNG image or an HTML page, is being transmitted. 
	- This will *affect the way a browser will process the file*.
- The content type, also called **MIME (Multipurpose Internet Mail Extensions) or Media types**, is a **statement in the response header of the web server** which gives the browser information regarding the format of a transmitted file. 
	- This enables the browser to work faster, as the type of file does not have to be identified after the file has arrived.
	- ![[attachments/Pasted image 20221107153020.png]]

> [!important]- Every *non-empty response* from a web server should contain a **Content-Type**

> [!caution]+ When should we use `Content-Type`
> - The **client** *sets a content type for the body it is sending to the server* (e.g. for Post or Put) to tell the server what type of data it is receiving.
> - The **server** sets a content type for the *response*.

> [!question]- What happens if I do not specify a content type?
> - If there is no indication as to the resource type of a file, the *browser must find this out itself*. 
> - Most current browsers will read the first bytes of the file and try to compare them with the most common content types.
> - This can work well, but it does not always. Especially in the case of texts with unusual character encoding, it can lead to a browser recognising text but incorrectly reproducing the special characters that it contains.

- There are sometimes instances in which the browser tries to recognise the content type even when that content has been defined. 
	- This is called **MIME sniffing**, and it can be prevented with a special response header, the [X-Content-Type-Options](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Content-Type-Options), through a nosniff directive.

- The MIME type is always displayed as a *combination of two pieces of information*, one indicating what type of medium it is and the other the sub-type of the medium. It looks like this:
	- `media type/sub-type`
	- Example: `text/css`, `text/javascript`, `text/xml`, `image/png`, `image/jpeg`

- `application` mime type:
	- The information communicated via application *tells the browser that the files are to be opened with a certain programme*. 
	- The most interesting aspect is `application/javascript`, which indicates that a server-side javascript file is to be executed.
	- YouTube uses this MIME type for its video pages, as a JavaScript video player is integrated there which then takes care of loading and playing the actual video files.

- Syntax:
```
Content-Type: text/html; charset=utf-8
Content-Type: multipart/form-data; boundary=something
```



## References
- [What is the HTTP Content Type? - SISTRIX](https://www.sistrix.com/ask-sistrix/onpage-optimisation/what-are-http-status-codes-and-what-types-are-there/what-is-the-http-content-type#:~:text=The%20content%20type%20status%2C%20part,browser%20will%20process%20the%20file.)