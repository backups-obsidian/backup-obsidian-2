---
created: 2022-05-15 16:34
updated: 2022-05-15 20:44
---
---
**Links**: [[104 Linux Index]]

---
## Good old packages
- `.deb` → used by debian and its derivatives like ubuntu.
- `.rpm` → red hat, fedora, opensuse and their derivatives.
- These contain the **binary version of the app**. There is *no source code*. 
- It is just *precompiled version of the application*. 
- It is compiled depending on the architecture of your system like arm, x86 etc.
- These come with a descriptor file inside the package which lets the system know which *libraries it needs* since these packages only package the application. *These repositories are then fetched at install*.
- Some problems with these packages are that the *developer needs to package the application for various distributions*.

## Universal Packaging formats
- **Snap**, **AppImage**, and **Flatpak**, all *distro independent*
- Flatpaks, Appimages and Snaps are also known as *universal packaging systems*.
- They *take away the dependency issues* many Linux users face every day and make life a lot easier for developers. However, this also results in *larger binaries*.

### Flatpak:
- It is also the *binary version of the application* i.e. they are also *precompiled*. 
- But they *ship with their own subset of libraries which means you don't need to rely on system's libraries to run*. 
- Some disadvantages are that *they take more disc space*. 
- Also since they come with their own libraries it is the developer's job to patch them on a regular basis otherwise there can be security issues.
- You can go to go to flathub for downloading flatpaks.
- Installing packages: `flatpak install flathub org.videolan.VLC`
	- Before running this command flatpak must be installed on the system.

### Snaps:
- These are also *binaries* with all the libraries included. 
- It is not really open. The snaps are available at snapcraft which is owned by canonical and they decide what goes in the store. 
- They are *bigger than flatpaks*. 
- They are mostly available on ubuntu and some ubuntu based distributions.
- *Self updating*.
- Installing packages: `sudo snap install vlc`
	- Before running this command snap must be installed on the system.

### Appimages:
- They ship the *whole application in one package*. 
- They ship the binaries, the run time and other things. 
- This means *you just need to download the file and click on it to start it*. 
- **No need to install anything**.
- AppImages are usually *faster than snaps or flatpaks and need less storage space*.
- They are also easy to remove as we have only have to delete a single file.
- Once we download an app image we have to make it executable before executing it.
	- `chmod u+x AppImage` to make the app image executable
	- `./AppImage` to run the image
- It is similar to `.exe` files in windows.

- Use cases: 
	- Great way to *try out new apps without installing* them on your machine.
	- An app is no longer maintained but users still want to install it on modern systems. In that case, I think it makes sense to "*preserve*" the app as something completely self-contained & guaranteed to run like an AppImage, so the developer never has to touch it again but users who want it can keep using it forever.

## Differences 
- Space: *deb < AppImage < Flatpaks / Snaps*
- For using snap and flatpak they must be installed in the system. So **if you don't have internet on the system you are out of luck**. In case of AppImages you don't have to install anything just run the file.
- *Updates*:
	- *Snap and Flatpak apps can be automatically updated* without user intervention. 
	- *AppImage lacks a robust automatic update mechanism*. 
	- **Users have to manually download new AppImage binary and replace existing one to install updates**.  
	- There is no hands-free update mechanism available for AppImage yet.
