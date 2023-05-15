---
created: 2023-05-15 12:42
updated: 2023-05-15 14:06
---
---
**Links**: [[104 Linux Index]]

---
## Netcat
- We can use netcat for **troubleshooting networking issues**.
- Command to test a basic connection for a service that has a particular IP and is listening on a specific port:
	- `nc -vz <IP-Address/Host-name> <port>`
	- `-z` option is used for reporting connection status without establishing a connection.
	- `-v` is for verbose mode.
- Sending a udp packet: `nc -u <host> <port>`
- Create a chat server:
	- `nc -l -p <port>`
	- Connecting to the chat server: `nc -v <host> <port>`

## References
- [How to test connectivity with Netcat - YouTube](https://www.youtube.com/watch?v=W2-phl3tY3o)
- [nc (Netcat) Command: Syntax, Command Options, & Examples (phoenixnap.com)](https://phoenixnap.com/kb/nc-command)