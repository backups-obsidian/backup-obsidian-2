---
created: 2022-07-26 17:10
updated: 2022-07-26 20:20
---
---
**Links**: [[300 home]]

---
### Scanning hosts
```bash
nmap 123.141.123.1
nmap domain-name.com
```

#### Multiple IP addresses
```bash
# multiple IP addresses
nmap ip1 ip2
```

#### Range of IP addresses
```bash
# range of IP addresses
nmap 123.123.123.1-10

# exclude certain IPs 
nmap 123.123.123.1-10 --exclude 123.123.123.4
```

### Viewing service or port information
```bash
nmap -sV 123.123.123.123
```

### Detecting the operating system
```bash
nmap -A 1.2.3.4
```

### Scan an entire subnet
```bash
nmap 1.2.3.4/24
```

The above command will take a lot of time depending on the number of devices in the network. To speed it up we can use the following option.

```bash
nmap -sP 1.2.3.4/24
```

### Faster scans
- T5 is one of the timing templates for `nmap`.
	- It is the **fastest**.
	- There might be some inaccuracies.

> [!note]- **T3 is the default** when no option is specified.

```bash
nmap -T5 1.2.3.4/24
```
