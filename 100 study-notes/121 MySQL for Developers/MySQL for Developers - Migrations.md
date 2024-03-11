---
created: 2024-03-09 13:16
updated: 2024-03-09 19:46
---
---
**Links**: [[121 MySQL for Developers Index]]

---
## Migrations
- Our schema will never be perfect because of factors outside our control like business requirements and so on.
	- We need migrations for keeping the database schema upto date.
- **Migrations are just scripts whose sole purpose is the migrate the database schema from one version to another**.
- For any language migration is performed by a migration tool.
	- Generally migration tools keep a version of the migration in the database and update that version when a new migration is performed.
	- Migration tool goes over all the migration scripts to bring the database to the desired state.

## References
- https://www.youtube.com/watch?v=dJDBP7pPA-o 
	- Overall idea
- https://www.youtube.com/watch?v=SdcH6IEi6nE
	- Using migrations in python with FastAPI
