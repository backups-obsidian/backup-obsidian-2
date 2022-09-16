---
created: 2022-09-15 21:09
updated: 2022-09-16 00:52
---
---
**Links**: [[111 KodeCloud Index]]

---
## Java
- Upgrade from version 8 to version 9 was not backward compatible and resulted in a lot of broken packages hence you will see a lot of companies still using Java 8.

- Java 8 version number is `1.8`. This changed with version 9.
	- ![[attachments/Pasted image 20220915221228.png]]

> [!question]+ What is a JDK?
> - JDK is a set of development tools which will help us *develop, build and run the java application*.
> - These tools are present in the `bin` directory of the jdk
> ![[attachments/Pasted image 20220915221906.png]]
> `jdb` is used for debugging java applications and `javac` is used for compiling java code.

- **JRE** (Java Runtime Environment) is used to **run the Java applications on any system**.
	- JRE and the `java` command line utility are the only components required to run already built Java applications on a system.

- Before version 9 of Java JDK and JRE were shipped as separate components.
	- So if you were not developing a Java application and just wanted to run them then you could have only downloaded the JRE.
	- ![[attachments/Pasted image 20220915222109.png]]
- **After version 9 JRE is packed into JDK**.
- JVM
	- ![[attachments/Pasted image 20220915222747.png]]

### Building & Packaging 
- JAR stands for Java Archive it helps compress and combine multiple Java class files and its dependencies into a single distributable package.
	- `jar cf JarFileName.jar file1 file2 ....`
- Incase of a web application we have static files like html and css. They are packaged in a *WAR (Web Archive)* file.
- When `jar` command is run it automatically generates a manifest file within the package at path `META-INF/MANIFEST.MF`
	- This file contains information about the files packaged in jar file and any other meta data about the application
	- ![[attachments/Pasted image 20220915230858.png]]
	- One important information present in the meta data is the entry point to the application.

- Once jar is built it can be run on any platform using `java -jar JarFileName.jar`
- Generating documentation for the code: `javadoc -d doc MyClass.java` 
	- ![[attachments/Pasted image 20220915231226.png]]
	
- When working with large software it can get really cumbersome to package and generate documentation for the project.
	- This is where build tools like *Maven, ANT and gradle* are used.

- ANT example:
	- ![[attachments/Pasted image 20220915231558.png]]
	- If we want to run specific sections of ANT then we can use `ant compile jar`


## NPM (JavaScript)
- When you install a npm package they are installed inside the `node_modules` folder in the current directory.
- A directory by the name of the package is created it contains different files like `README.md`, `LICENSE`, `package.json` and the `lib` folder contains the code.
	- ![[attachments/Pasted image 20220915234344.png]]
- The `package.json` inside the package is the file which contains meta data for the package like its name, version, author, its *git repository*.
	- This file is useful when you are troubleshooting issues related to dependency.

> [!note]- When we import a package node first looks for the package in the local `node_modules` directory if the package is not found then it looks in other locations.
> We can find the look up order by priority using the following command.
> ![[attachments/Pasted image 20220915234559.png]]

- We can install the package globally using: `npm -g install <package-name>`
- There are two types of modules: 
	- *BuiltIn*: These are installed automatically when nodejs is installed for the first time.
	- External

## Python
- ![[attachments/Pasted image 20220915211426.png]]
- Python versions:
	- Python2: 2000 - 2010
	- Python3: 2008 - Till Date.
- When we install Python PIP gets installed automatically. 
- If we have multiple python versions then we will have different pips.
	- ![[attachments/Pasted image 20220916002328.png]]

> [!important]- So if you don't know to which python pip is pointing just use `pip -V`

- When Python is installed it creates the following package structure.
	- A directory is created for each version of Python under the `/usr/lib` directory.
- When we install a python package using pip it places it in the `site-packages` folder in the appropriate python folder.
	- This may not be completely true as they can also be installed in different locations.
- Packages can also be 32 bit or 64 bit so a separate path is available.
	- ![[attachments/Pasted image 20220916003429.png]]

> [!important]+ Finding the location of a particular package
> `pip show <package-name>`
> ![[attachments/Pasted image 20220916004310.png]]

- When importing a package python looks at a list of paths to find and import the package.
	- We can list the paths using `print(sys.path)`
	- ![[attachments/Pasted image 20220916004511.png]]

- To install a lot of packages we use `requirements.txt`
	- `pip install -r requirements.txt`
- It is a best practice to specify the version of packages in `requirements.txt` file.
	- *If no version is specified then python will install the latest version of the package*.
	- ![[attachments/Pasted image 20220916004805.png]]
- Upgrading a package: `pip install <package-name> --upgrade`
- Uninstalling a package: `pip uninstall <package-name>`
- Other package managers:
	- Eggs don't need to be unpacked but they are obsolete
	- Wheels are similar to eggs but they need to be unpacked.

- To install a wheel package we do `pip install package.whl`
	- ![[attachments/Pasted image 20220916005133.png]]
