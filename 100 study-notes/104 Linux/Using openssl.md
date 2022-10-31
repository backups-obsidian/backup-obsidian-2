---
created: 2022-10-15 10:10
updated: 2022-10-31 01:18
---
---
**Links**: 
- [[104 Linux Index]]
- [[../111 KodeCloud/KodeCloud - SSL-TLS | KodeCloud - SSL - TLS]]

---
## Openssl
- Get the `openssl` version: `openssl version -a`
---

- Creating self signed certificates.
	- This command tells openssl to *create a private key* and *a new CSR* at the same time.
	- If we are using `-x509` option it will *output a certificate instead of a CSR*.
	- Here 365 days is the validity of the certificate.
- The private key is encrypted using the passphrase whereas the public key is only base64 encoded. 
- Displaying the information of a certificate
	- `openssl x509 -in ca-cert.pem -noout -text`

- Creating a key for the webserver.
	- We remove the `-x509` option since we are not self signing the key and just creating a CSR.
	- We also remove the `-days` option since we are generating a CSR and not a certificate.
	- `openssl req -newkey rsa:4096 -keyout server-key.pem -out server-req.pem -subj "/C=FR/ST=Ile..."`
	- Here `server-key.pem` is the private key and `server-req.pem` is the CSR.

- Signing server's certificate with CA's private key. We use CA's private as well as public certificate.
	- `openssl x509 -req -in server-req.pem-CA ca-cert.pem -CAkey ca-key.pem -CAcreateserial -out server-cert.pem`
	- `-req` and `x509` tells openssl that it is a CSR.
	- `-CAcreateserial` ensures unique serial number for each CSR.
	- By default the certificate is only valid for 30 days. We can change it by adding the `-days` option to the signing command.

### Generating a set of public and private keys
- `openssl genrsa`: This command will just output the key to the terminal.

> [!note]- **The key generated using `openssl genrsa` contains both the public and the private key**.

- To *save the key* we use: 
	- `openssl genrsa -out privatekey.pem`
- Default key size is generally *2048*. This can be set in the openssl configuration file.
	- `openssl genrsa -out privatekey.pem 4096`
- **Extracting the public key from the key pair**:
	- `openssl rsa -in privatekey.pem -pubout -out publickey.pem`
- The most common format for openssl is `.pem`
	- The binary contents of the file is base64 encoded hence it is super easy to copy and paste.
- *Inspecting* the private key.
	- `openssl rsa -in privatekey.pem -noout -text`
- *Inspecting* the public key.
	- `openssl rsa -pubin -in publickey.pem -noout -text`

> [!question]- What is the difference between the public and the private key?
> - If we take a look at the private key it will contain the *modulus and the exponent* and a lot of other things. 
> - The *public key only includes the modulus and the exponent*.
> ![[attachments/Pasted image 20221031010924.png]]
> ![[attachments/Pasted image 20221031010822.png]]
	
- *We can keep on increasing the size of RSA keys for more security but it is not ideal for IOT devices*.
	- Key size of 512 is easily brute forcible and 2048 is also not safe.
- With **elliptic curves we can increase our security without increasing our memory footprint**.
- We can have *RSA or elliptic keys*.
- If we take a look at the contents of the RSA private key using `cat` it will say `--BEGIN RSA PRIVATE KEY--` in the beginning.

### Certificates & Generating Certificates
> [!important]+ Both **CSRs and self signed certificates are generated using the `req`** option.
> The only difference is the `-x509` option which is used to generate self signed certificates.

- Generating a CSR from a private key:
	- `openssl req -new -key privatekey.pem -out CSR.pem`
	- If we take a look at the contents of the CSR using `cat` it will say 
- Generating a Self Signed Certificate from a private key:
	- `openssl req -x509 -key privatekey.pem -out CERT.pem`
	- If we take a look at the contents of the Certificate using `cat` it will say `--BEGIN CERTIFICATE--` in the beginning.

- `req` stands for request. 
	- It is used to *create* and *process* **certificate requests**.
- `x509` is a standard format for defining public key certificates.

- Certificates are there to identify websites.
- The *websites are identified using* the **DN (Distinguished Name)** format.
	- DN format is a hierarchy of key value pairs.
- **CN (common name) has to be there in all the certificates**.
	- This has to *match the domain name* which we enter in the browser *or the Subject Alternative Name* (SAN certificates). **(DOUBT)**
	- Rest of the information like country, locality, company name etc is just for informational purpose. 

- The *details we enter while generating a CSR or a Self Signed Certificate* goes into the **subject field** of the certificate.

> [!important]- In case self signed certificates *issuer* and *subject* will be the same.
> The official definition of self signed certificates is that the issuer field matches the subject field.

- Looking at the *contents of the certificate*:
	- `openssl x509 -in CERT.pem -noout -text`
- Extracting *specific features of information*:
	- `openssl x509 -in CERT.pem -noout -text -subject -issuer`
	- `openssl x509 -in CERT.pem -noout -text -dates`: Validity dates

- Viewing the *contents of the CSR*:
	- `openssl req -in CSR.pem -noout -text`
	- There will be no issuer.

- Sample output of a self signed certificate:
	- ![[attachments/Pasted image 20221031002415.png]]

- Whenever we generate a CSR or a self signed certificate we have to enter the *subject values* interactively. We can pass it as *an argument*.
	- `openssl req -new -key privatekey.pem -out CSR.pem -subj "/CN=website.com"`
	- For self signed certificates we have `-x509` instead of `-new`
	- Specifying just the CN to keep it simple.

### How to link a private key file with a particular certificate
- This is done by matching the public key value in the private key and in the certificate or the CSR.
	- In particular we match the modulus value

- Modulus value of a CSR:
	- `openssl req -in CSR.pem -noout -modulus`
- Modulus value of a certificate:
	- `openssl x509 -in CERT.pem -noout -modulus`
- Modulus value of a private key:
	- `openssl rsa -in privatekey.pem -noout -modulus`

> [!note]- Notice that we have to use `req` to list the contents of a CSR whereas we use `x509` to list the contents of a certificate.
---
### Example
- Sample certificate of `sarthaknarayan.tech`
	- ![[attachments/Pasted image 20221031003634.png]]
	- CN is a `*.sarthaknarayan.tech`
	- ![[attachments/Pasted image 20221031003727.png]]
	- ![[attachments/Pasted image 20221031003751.png]]

## References
- [Create & sign SSL/TLS certificates with openssl - YouTube](https://www.youtube.com/watch?v=7YgaZIFn7mY)