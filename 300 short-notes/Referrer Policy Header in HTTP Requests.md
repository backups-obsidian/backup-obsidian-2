---
created: 2023-03-08 17:42
updated: 2023-03-08 17:56
---
---
**Links**: [[300 home]]

---
- HTTP requests include the optional `Referer` header, which *indicates the origin or the web page URL from which the request was originated*.
- The value determines *how much information*, if any, is disclosed via the Referer Request header.
- Do not disclose the referring page URL unless there is an business need: `Referrer-Policy: no-referrer`
	- ![[attachments/Pasted image 20230308174402.png]]
- We can get the referrer value using `document.referrer`
	- For example if I go to my blog website from a LinkedIn post I get this output
		- ![[attachments/Pasted image 20230308175447.png]]
- References:
	- [When To Use noreferrer In Your Links (with referrer policy examples) - YouTube](https://www.youtube.com/watch?v=4Cnp-q54Ulo)
	- [How Referral traffic works - Referrer Policy - YouTube](https://www.youtube.com/watch?v=8oGlkqq_0Ao)