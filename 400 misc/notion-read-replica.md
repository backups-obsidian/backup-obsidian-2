---
created: 2022-04-16 19:25
updated: 2022-04-17 11:58
---

# Idea
- Creating an offline read mode for Notion

# Features
- Completely serverless.
- No intervention needed

# How will you do it
- Figure out the endpoint from notion web to which export notion calls are made.
- Get the cookies for making the calls since this call cannot be made from the API
- Schedule a cron job in GitHub Actions to run a docker container.
- This docker container will get make the call to get the export. 
- Make an API call to upload it to google drive.
- To sync it back to desktop you will need Google Drive for desktop
- The sync should be incremental. You should be able to figure out what has changed and only sync those pages instead of syncing the whole export. 

# Tech Stack/Keywords
- Go/Python. Go is preferred so that you can have projects to show.
- GitHub Actions
- Google Drive
- Docker
- CI/CD
- DevOps

# References
# Future enhancements
- Make an all AWS version of this project by using Lambda and EventBridge(cron jobs)

