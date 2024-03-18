---
created: 2024-03-18 12:47
updated: 2024-03-18 12:48
---
- Fixing apple clipboard handover:
	- https://discussions.apple.com/thread/254811288?sortBy=best

- Run the following command in the terminal
```bash
defaults delete ~/Library/Preferences/com.apple.coreservices.useractivityd.plist ClipboardSharingEnabled
defaults write ~/Library/Preferences/com.apple.coreservices.useractivityd.plist ClipboardSharingEnabled 1
```