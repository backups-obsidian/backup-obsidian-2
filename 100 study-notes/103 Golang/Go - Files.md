---
created: 2022-05-29 09:32
updated: 2022-05-29 10:45
---
---
**Links**: [[103 Golang Index]]

---
- We use the package `os` to work with files
	- It provides platform independent interface
	- It is intended to be uniform across all OSes which means the programs we create work the same on Linux, Windows and Mac.
```go
var newFile *os.File
var err error
newFile, err = os.Create("a.txt")
if err != nil {
	log.Fatal(err) // recommended way of handling errors
} 
// when we are done with the file we must close it 
newFile.close()

// opening a file
file, err := os.Open("a.txt")
```
- `log.Fatal` should be used for printing errors since its thread safe and also prints out the timing information
	- It will also exit the program with a status code of 1

## Creating, Renaming, Deleting etc
```go
/////////////////////////////////
// Creating, Opening, Closing, Renaming, Moving, and Removing files in Go
/////////////////////////////////

package main

import (
	"fmt"
	"log"
	"os"
)

func main() {

	//** Use valid paths according to your OS. **//

	// CREATING A FILE

	// os.Create() function creates a file if it doesn't already exist. If it exists, the file is truncated.
	// it returns a file descriptor which is a pointer to os.File and an error value.
	newFile, err := os.Create("a.txt")

	// error handling
	if err != nil {
		// log the error and exit the program
		log.Fatal(err) // the idiomatic way to handle errors

	}

	// TRUNCATING A FILE
	err = os.Truncate("a.txt", 0) //0 means completely empty the file.

	// error handling
	if err != nil {
		log.Fatal(err)
	}

	// CLOSING THE FILE
	newFile.Close()

	// OPEN AND CLOSE AN EXISTING FILE
	file, err := os.Open("a.txt") // open in read-only mode

	// error handling
	if err != nil {
		log.Fatal(err)
	}
	file.Close()

	//OPENING a FILE WITH MORE OPTIONS
	file, err = os.OpenFile("a.txt", os.O_APPEND, 0644)
	// We can Use opening attributes individually or combined
	// using an OR between them
	// e.g. os.O_CREATE|os.O_APPEND
	// or os.O_CREATE|os.O_TRUNC|os.O_WRONLY
	// os.O_RDONLY // Read only
	// os.O_WRONLY // Write only
	// os.O_RDWR // Read and write
	// os.O_APPEND // Append to end of file
	// os.O_CREATE // Create is none exist
	// os.O_TRUNC // Truncate file when opening

	// error handling
	if err != nil {
		log.Fatal(err)
	}
	file.Close()

	// GETTING FILE INFO
	var fileInfo os.FileInfo
	fileInfo, err = os.Stat("a.txt")

	p := fmt.Println
	p("File Name:", fileInfo.Name())        // => File Name: a.txt
	p("Size in bytes:", fileInfo.Size())    // => Size in bytes: 0
	p("Last modified:", fileInfo.ModTime()) // => Last modified: 2019-10-21 16:16:00.325037748 +0300 EEST
	p("Is Directory? ", fileInfo.IsDir())   // => Is Directory?  false
	p("Pemissions:", fileInfo.Mode())       // => Pemissions: -rw-r-----

	// CHECKING IF FILE EXISTS
	fileInfo, err = os.Stat("b.txt")
	// error handling
	if err != nil {
		if os.IsNotExist(err) {
			log.Fatal("The file does not exist")
		}
	}

	// RENAMING AND MOVING A FILE
	oldPath := "a.txt"
	newPath := "aaa.txt"
	err = os.Rename(oldPath, newPath)
	// error handling
	if err != nil {
		log.Fatal(err)
	}

	// REMOVING A FILE
	err = os.Remove("aa.txt")
	// error handling
	if err != nil {
		log.Fatal(err)
	}

}

```

## Writing to a file
- For writing a string to file we convert it to a byte slice or rune slice if it contains non ASCII character.
### Using os and ioutil
```go
/////////////////////////////////
// Writing Bytes to Files
/////////////////////////////////

package main

import (
	"io/ioutil"
	"log"
	"os"
)

func main() {

	// opening the file in write-only mode if the file exists and then it truncates the file.
	// if the file doesn't exist it creates the file with 0644 permissions
	file, err := os.OpenFile(
		"b.txt",
		os.O_WRONLY|os.O_TRUNC|os.O_CREATE,
		0644,
	)
	// error handling
	if err != nil {
		log.Fatal(err)
	}
	// defer closing the file
	defer file.Close()

	// WRITING BYTES TO FILE

	byteSlice := []byte("I learn Golang! 传")   // converting a string to a bytes slice
	bytesWritten, err := file.Write(byteSlice) // writing bytes to file.
	// It returns the no. of bytes written and an error value
	// error handling
	if err != nil {
		log.Fatal(err)
	}
	log.Printf("Bytes written: %d\n", bytesWritten) // => 2019/10/21 16:26:16 Bytes written: 19

	// WRITING BYTES TO FILE USING ioutil.WriteFile()

	// ioutil.WriteFile() handles creating, opening, writing a slice of bytes and closing the file.
	// if the file doesn't exist WriteFile() creates it
	// and if it already exists the function will truncate it before writing to file.

	bs := []byte("Go Programming is cool!")
	err = ioutil.WriteFile("c.txt", bs, 0644)
	// error handling
	if err != nil {
		log.Fatal(err)
	}
}

```

### Using buffio (buffered writer)
- It lets us create a **buffered writer** so that we can **write to buffer in memory** before writing it to disk.
- This is specially useful if *we need to do lot of manipulations on the data* before writing it to disk.
	- We can save a lot of disk io time
- It is also useful if we want to write to a file byte by byte. 
	- We store a large number of bytes in memory before writing it to the file.
	- This is faster since disk operations are slow. 
- The default size of buffer is *4096 bytes*

```go
/////////////////////////////////
// Writing to Files using a Buffer in Memory
//
/////////////////////////////////

package main

import (
	"bufio"
	"log"
	"os"
)

func main() {

	// Opening the file for writing
	file, err := os.OpenFile("my_file.txt", os.O_WRONLY|os.O_CREATE, 0644)
	// error handling
	if err != nil {
		log.Fatal(err)
	}
	// defer closing the file
	defer file.Close()

	// Creating a buffered writer from the file variable using bufio.NewWriter()
	bufferedWriter := bufio.NewWriter(file)

	// declaring a byte slice
	bs := []byte{97, 98, 99}

	// writing the byte slice to the buffer in memory
	bytesWritten, err := bufferedWriter.Write(bs)

	// error handling
	if err != nil {
		log.Fatal(err)
	}
	log.Printf("Bytes written to buffer (not file): %d\n", bytesWritten)
	// => 2019/10/21 16:30:59 Bytes written to buffer (not file): 3

	// checking the available buffer
	bytesAvailable := bufferedWriter.Available()
	log.Printf("Bytes available in buffer: %d\n", bytesAvailable)
	// => 2019/10/21 16:30:59 Bytes available in buffer: 4093

	// writing a string (not a byte slice) to the buffer in memory
	bytesWritten, err = bufferedWriter.WriteString("\nJust a random string")

	// error handling
	if err != nil {
		log.Fatal(err)
	}

	// checking how much data is stored in buffer, just  waiting to be written to disk
	unflushedBufferSize := bufferedWriter.Buffered()
	log.Printf("Bytes buffered: %d\n", unflushedBufferSize)
	// -> 24 (3 bytes in the byte slice + 21 runes in the string, each rune is 1 byte)

	// The bytes have been written to buffer, not yet to file.
	// Writing from buffer to file.
	bufferedWriter.Flush()
}

```

## Reading from a file
### Using io and ioutil
```go
/////////////////////////////////
// Reading Files in Go
/////////////////////////////////

package main

import (
	"fmt"
	"io"
	"io/ioutil"
	"log"
	"os"
	"strings"
)

func main() {

	//** READING INTO A BYTE SLICE USING io.ReadFull() **//

	// Opening the file in read-only mode. The file must exist (in the current working directory)
	// Use a valid path!
	file, err := os.Open("test.txt")
	if err != nil {
		log.Fatal(err)
	}
	defer file.Close()

	// declaring a byte slice and initializing it with a length of 2
	byteSlice := make([]byte, 2)

	// io.ReadFull() returns an error if the file is smaller than the byte slice.
	// it reads the file into the byte slice up to its length
	numberBytesRead, err := io.ReadFull(file, byteSlice)
	if err != nil {
		log.Fatal(err)
	}
	log.Printf("Number of bytes read: %d\n", numberBytesRead)
	log.Printf("Data read: %s\n", byteSlice)

	fmt.Println(strings.Repeat("#", 20))

	//** READING WHOLE FILE INTO A BYTESLICE USING ioutil.ReadAll() **//

	// Opening another file (from the current working directory)
	file, err = os.Open("main.go")
	if err != nil {
		log.Fatal(err)
	}

	// ioutil.ReadAll() reads every byte from the file and return a slice of unknown size
	data, err := ioutil.ReadAll(file)
	if err != nil {
		log.Fatal(err)
	}

	fmt.Printf("Data as string: %s\n", data)
	fmt.Println("Number of bytes read:", len(data))

	//** READING WHOLE FILE INTO MEMORY USING ioutil.ReadFile() **//

	// ioutil.ReadFile() reads a file into byte slice
	// this function handles opening and closing the file.
	data, err = ioutil.ReadFile("test.txt")
	if err != nil {
		log.Fatal(err)
	}
	log.Printf("Data read: %s\n", data)
}
```

### Using buffio
- Reading line by line separated by a delimiter
```go
/////////////////////////////////
// Reading Files Line by Line (or using a delimiter) using bufio.Scanner
// Go Playground: https://play.golang.org/p/O3szMQZ7QZX
/////////////////////////////////

package main

import (
	"bufio"
	"fmt"
	"log"
	"os"
)

func main() {

	// opening the file in read-only mode. The file must exist (in the current working directory)
	// use a valid path!
	file, err := os.Open("my_file.txt")
	// error handling
	if err != nil {
		log.Fatal(err)
	}
	// defer closing the file
	defer file.Close()

	// the file value returned by os.Open() is wrapped in a bufio.Scanner just like a buffered reader.
	scanner := bufio.NewScanner(file)

	// the default scanner is bufio.ScanLines and that means it will scan a file line by line.
	// there are also bufio.ScanWords and bufio.ScanRunes.
	// scanner.Split(bufio.ScanLines)

	// scanning for next token in this case \n which is the line delimiter.
	success := scanner.Scan() //read a line
	if success == false {
		// false on error or EOF. Check for errors
		err = scanner.Err()
		if err == nil {
			log.Println("Scan was completed and it reached End Of File.")
		} else {
			log.Fatal(err)
		}
	}

	// Getting the data from the scanner with Bytes() or Text()
	fmt.Println("First Line found:", scanner.Text())
	//If we want the next token, so the next line or \n, we call scanner.Scan() again

	// Reading the whole remaining part of the file:
	for scanner.Scan() {
		fmt.Println(scanner.Text())
	}

	// Checking for any possible errors:
	if err := scanner.Err(); err != nil {
		log.Fatal(err)
	}
}

```

## Reading from stdin (console)
```go
/////////////////////////////////
// Reading From Standard Input (console)
/////////////////////////////////

package main

import (
	"bufio"
	"fmt"
	"log"
	"os"
)

func main() {

	// creating a scanner
	scanner := bufio.NewScanner(os.Stdin) //os.Stdin reads the output from the command line
	fmt.Printf("%T\n", scanner)           //pointer to bufio.scanner

	scanner.Scan() //it waits for the input and buffers the input untill a new line

	// gettting the scanned data
	text := scanner.Text()   // string type
	bytes := scanner.Bytes() // uint8[] slice type

	fmt.Println("Input text:", text)
	fmt.Println("Input bytes:", bytes)

	// reading the input continously until a specific string is scanned
	for scanner.Scan() {
		text = scanner.Text()
		fmt.Println("You entered:", text)
		if text == "exit" {
			fmt.Println("Exiting the scanning ...")
			break
		}
	}

	// error handling
	if err := scanner.Err(); err != nil {
		log.Println(err)
	}
}

```