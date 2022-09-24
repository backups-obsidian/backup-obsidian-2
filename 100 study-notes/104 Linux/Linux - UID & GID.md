---
created: 2022-09-23 23:18
updated: 2022-09-23 23:28
---
---
**Links** [[104 Linux Index]]

---
## UID & GID
- UID: User Identifier
- GID: Group Identifier
- A **UID (user identifier)** is a number assigned by Linux to each user on the system. 
	- This number is used to *identify the user to the system* and to *determine which system resources the user can access*. 
	- It is user's representation in the linux kernel.
- UIDs are stored in the **/etc/passwd** file:

> [!note]- Root has a UID and GID of **0**.
> - These numbers are what give the root account its overwhelming power. If you don't believe me, rename the root account to `goonygoogoo`, or whatever you choose, and then create a new user account named `root`, allowing the system to assign the next available UID and GID to it. This account has no more power than any other user account on the system. 
> - It's not the name, but the UID and the GID that give the administration account its power. To further test this assertion, assign a test user account with 0 for the UID and GID, and that user is now root, regardless of the account name.

- Most Linux distributions reserve the first **100** UIDs for system use. 
- New users are assigned UIDs starting from **500** or **1000**. 
	- For example, new users in Ubuntu start from **1000**:
	- The theory behind this arbitrary assignment is that anything below 1000 is reserved for system accounts, services, and other special accounts, and regular user UIDs and GIDs stay above 1000.
	- ![[attachments/Pasted image 20220923232709.png]]
- Groups in Linux are defined by **GIDs** (**group IDs**). 
- Just like with UIDs, the first **100** GIDs are usually reserved for system use. 
- GIDs are stored in the **/etc/groups** file
- We can find the id of the user using `id`
- Processes have owners just like files. 
	- *Only the owner (or the root user) of a process can send process signals to it*.

## References
- [Linux sysadmin basics: User account management with UIDs and GIDs | Enable Sysadmin (redhat.com)](https://www.redhat.com/sysadmin/user-account-gid-uid)