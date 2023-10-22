---
created: 2023-07-30 08:36
updated: 2023-07-30 09:14
---
---
**Links**: [[104 Linux Index]]

---
## What is ACME?
- **ACME (Automated Certificate Management Environment)** is a standard protocol for **automated domain validation** and installation of X.509 certificates.
- When we request TLS certificates from `LetsEncrypt` (**CA**) using `Certbot` (**ACME Client**) we use the ACME protocol.
	- Its not necessary to use `Certbot`, we can use other ACME clients also.
	- Example of another client: [acmesh-official/acme.sh: A pure Unix shell script implementing ACME client protocol (github.com)](https://github.com/acmesh-official/acme.sh) 
- There are 2 different domain validation methods:
	- *HTTP-01 Challenge Method*
	- *DNS-01 Challenge Method*

### HTTP-01 Challenge
- **HTTP-01** is the most commonly-used challenge method used with ACME and `Certbot`. 
- The HTTP-01 challenge requires you or your *ACME client* (`Certbot`) to *create a file containing a random token and fingerprint* of your account key on your web server, proving control over the website to the CA. 
- The *challenge specifies both the contents of the file, and the URL where it should be created* (which will always be prefixed with `.well-known/acme-challenge/`, followed by the token value).
- An example manual HTTP-01 challenge for `example.com` is shown below:
	- ![[attachments/Pasted image 20230730084408.png]]
- *The primary advantages are ease of automation for popular web server platforms like Apache and Nginx*, and the lack of any need to configure DNS records and wait for them to propagate.
- Limitations:
	- The HTTP-01 challenge **only works over port `80`**, so it CANNOT be used if this port is blocked on your web server.
	- *If there are multiple servers for a domain name, the HTTP-01 challenge file must be placed on all of them*.
	- **This challenge CANNOT be used to provision wild card certificates**.

### DNS-01 Challenge
- The DNS-01 challenge requires you to **create a DNS TXT record for your domain**, including a random token and fingerprint of your account key, at `_acme-challenge.<YOUR_DOMAIN>`.
- This is an example manual DNS-01 challenge for `example.com`
	- ![[attachments/Pasted image 20230730084731.png]]
- The DNS-01 challenge is *more difficult to automate than HTTP-01*, requiring that your **DNS provider supply an API for managing your DNS records**. 
	- In this case, you will also need to deal with the *potential security threat of keeping DNS API credentials on your web server*.
		- Best practice is to use more narrowly scoped API credentials, or perform DNS validation from a separate server and automatically copy certificates to your web server.
	- *Since automation of issuance and renewals is really important, it only makes sense to use DNS-01 challenges if your DNS provider has an API* you can use to automate updates.
- With the DNS-01 challenge, you will also need to *need to check for propagation of your record or configure a delay into your ACME client (`Certbot`) after creating the record*. 
- However, there are several circumstances where you might choose DNS-01 over HTTP-01:
	- If your *domain has more that one web server, you will not have to manage challenge files on multiple servers*.
	- DNS-01 *can be used even if port `80` is blocked on your web server*.
	- We **can issue wild card certificates**.

## References
- [Challenge Types - Let's Encrypt (letsencrypt.org)](https://letsencrypt.org/docs/challenge-types/#:~:text=DNS%2D01%20challenge,you%20to%20issue%20wildcard%20certificates.)
- [Quick and Easy SSL Certificates for Your Homelab! - YouTube](https://www.youtube.com/watch?v=qlcVx-k-02E): *Must watch*
	- The idea is that we can modify our DNS records to point to the a private IP in our network. If someone tries to access it will give an error since it is an internal IP.