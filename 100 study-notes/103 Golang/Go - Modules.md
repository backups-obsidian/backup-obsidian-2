---
created: 2022-06-05 21:11
updated: 2022-06-06 17:01
---
---
**Links**: [[103 Golang Index]]

---
## Modules
- A **module is a collection of Go packages** stored in a file tree with a `go.mod` file at its root.
	- It is like `package.json` in npm and is used for dependency management.
	- We *can use different versions of the same dependency*.
- Basic structure of the `go.mod` file
```go
module <name>
go <version>
require <module-path> <version>
```
- `go mod init name` to initialise a go module
	- Typically its `host hosting the code/user or organisation/repo name`: `github.com/user/repo`
- `go mod tidy`: to add the third party and remove packages from `go.mod` file as and when you add and remove third party imports from your source files.
- If we want to get some modules we can use `go get ...` 

> [!note]- *Module name is the import path prefix* for all packages within the module (local packages).

- Go modules that are downloaded are *stored locally* at `$GOPATH/pkg/mod`. 
	- Also known as module caching to prevent repeated downloads of the module.
	- If you are downloading files from github then they will be inside the directory `github.com`

- **We import the package and not the module**. So provide the git path to the package. 
	- ![[attachments/Pasted image 20220606163859.png]]
	- ![[attachments/Pasted image 20220606163935.png]]
	- But the `go.mod` file will have something like this  `require github.com/ddadumitrescu/go_math v1.1.0` since we download the full repo.
- If we are doing a `go get` then we provide the path to the repo.
- Updating the dependencies `go get -u`: this *will only work for minor updates and not patches and major updates*.
- `//indirect` in the `go.mod file` means no file is using that package.

- We can use *different versions of the same package* using **aliases**.
```go
import (
	packagev1 "github.com/user/repo/package"
	packagev2 "github.com/user/repo/v2/package"
)
```

> [!question]- A new version of a Go package that you are using in an application was released. How do you *update to the new version*?
> By **manually editing `go.mod`** or **running `go get`** command.

### Sample Project Structure
![[attachments/Pasted image 20220605211214.png]]
![[attachments/Pasted image 20220605210033.png]]
![[attachments/Pasted image 20220605210054.png]]

> [!note]- **While importing we are importing by directory name and not the package name**. But while using anything inside the source code we are using package name.

- Example
	- ![[attachments/Pasted image 20220605212743.png]]
	- ![[attachments/Pasted image 20220605212826.png]]
	- Better to use directory name as the package name

## Compiling and Running Programs
- `go run main.go` is used to compile and run the file. It doesn't create any executable.
- `go build` creates an executable with the same name as directory inside the directory. 
	- If you want to give the executable another option then you use `go build -o other_name`
	- In a directory that contains many Go source files you run `go build`. It will compile all the source files in the current directory and will produce an executable with the name of the directory.
- We can compile the executable binary for any OS in any OS using the `GOOS` and `GOARCH` parameters.
	- `GOOS=linux GOARCH=amd64 go build -o linuxapp`
- `gofmt` is formatting the go files according to a set standard. Eg: `gofmt -w main.go` here -w means it will overwrite the source file.
	- You can also run it on the whole directory `gofmt -w directory_path`
	- or you can run `go fmt` in the parent directory and it will format the code of all the go files.