---
created: 2024-01-13 19:10
updated: 2024-01-13 19:25
---
---
**Links**: [[119 System Design Index]]

---
## Consistent Hashing
- Generally used for sharding a database and deciding which key will go to which database server.
- If one of the nodes go down we will have to rehash all the keys.
- We map each of the servers to an area in the hash ring, we do the same thing for the keys.
- We go in clockwise direction and the first node in the clockwise direction of the key is the node of the key.
