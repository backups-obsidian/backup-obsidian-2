---
created: 2022-04-22 22:12
updated: 2023-01-30 23:29
---
---
**Links**: [[Projects]]

---
- I already **sync** it up using GDrive.
- I want to back it up using GitHub or make it a compressed zip at the end of the day.
- If I want to upload it to GitHub I would want to do it in a different account. 
- Also I like the point that use Gdrive for sync and use something else for backup in which you can't delete things.

## Conclusion
- Using GitHub for backup with a custom script. 
- I didn't want to manage SSH keys so I use username and personal access key in the git push URL. I know it is not a good approach but it solves a lot of hassle. Also my vault will never have personal information.
```bash
#!/bin/bash

git_base_path="/Users/${USER}/Desktop/github/backup-obsidian"
vault_path="/Users/${USER}/Documents/Obsidian vault/."
github_username="xxxxx"
github_key="xxxxx"

echo "copying"
cp -a "${vault_path}" "${git_base_path}"

echo "executing git commands"
git -C $git_base_path add -A
git -C $git_base_path commit -m "Update"
git -C $git_base_path push "https://${github_username}:${github_key}@github.com/backups-obsidian/backup-obsidian"
```
- I also use a custom script for backing up my `.obsidian` settings.
```bash
#!/bin/bash

git_base_path="/Users/${USER}/Desktop/github/Obsidian-vault-settings"
vault_path="/Users/${USER}/Documents/Obsidian vault/main/.obsidian/."
git_path="${git_base_path}/obsidian-settings/"

echo "copying"
cp -a "${vault_path}" "${git_path}"

echo "executing git commands"
git -C $git_base_path add -A
git -C $git_base_path commit -m "Update"
git -C $git_base_path push
```