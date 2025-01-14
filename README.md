# LETUS_prototype Go-wrapper
![LICENSE](https://img.shields.io/badge/License-MIT-brightgreen) 
![Language](https://img.shields.io/badge/Language-C%2B%2B17-blue)
![Language](https://img.shields.io/badge/Language-Go-blue)


This repositary provides a go-wrapper for LETUS_prototype.
LETUS_prototype is a prototype implementation of [LETUS: A Log-Structured Efficient Trusted Universal BlockChain Storage](https://doi.org/10.1145/3626246.3653390).
LETUS is a <u>**L**</u>og-structured <u>**E**</u>fficient <u>**T**</u>rusted <u>**U**</u>niversal <u>**S**</u>torage for blockchain, providing cryptographic tamper evidence with excellent performance and resource efficiency.
LETUS is made up of three main components: DMM-Trie, LSVPS, VDLS.
- **DMM-Trie** is a central component within the storage layer, functioning as a multi-versioned Merkle tree that provides cryptographic verification with dynamic updates and deterministic hashes.
- **LSVPS** is a log-structured, versioned, page abstraction storage subsystem used to store a large volume of pages generated by DMM-Trie.
- **VDLS** is an append-only logging stream that stores all versioned user data records.

The following figure shows the architecture of LETUS.
<img src="./README.assets/Architecture.png" alt="LETUS" style="zoom:50%;" />

## Dependencies
Developers are required to install the following dependencies:
* [Go](https://golang.org/) >= 1.21
* [OpenSSL](https://www.openssl.org/) >= 3.0.14


# Get Started
To use LETUS_prototype through this go wrapper, developers first need to create a project directory, and initailize the go module.
```
$ mkdir test_letus/
$ cd test_letus
$ go mod init test_letus
$ go get github.com/fishfishfishfishfish/LETUS-go/letus
$ mkdir data/
```
The above commands will create files `go.mod` and `go.sum`. The project directory should look like this:
```
.
├── data/               // data path, create by the developer
├── main.go             // a simple test for the go-wrapper
├── go.mod
└── go.sum
```
The `main.go` is the main go written by developer. A example of the main.go can look like this:

```go
// in main.go
package main

import (
	"fmt"
	"github.com/fishfishfishfishfish/LETUS-go/letus"
)

func main() {
    config := letus.GetDefaultConfig()
    db, ok := letus.NewLetusKVStroage(config)  // initialize the letus database
    if ok != nil{
        panic("Failed to create LetusKVStroage")
    }
    batch, _ := db.NewBatch()  // create a batch for updates

    // batch operation
    batch.Put([]byte("11111"), []byte("aaaaa"))
    batch.Put([]byte("22222"), []byte("bbbbb"))
    ok = batch.Delete([]byte("22222"))
    seq, _ = db.GetSeqNo()
    // commit a batch
    batch.Hash(seq)
    batch.Write(seq)
    batch.Release()

    // db get operations
    res, _ = db.Get([]byte("55555"))
    if res == nil { 
        fmt.Println("Failed to get") 
    } else { fmt.Println(string(res)) }

    // db proof operation.
    seq, _ = db.GetStableSeqNo()
    db.Proof([]byte("11111"), seq)

    // close db
    _ = db.Close()
}
```

## Build & Run
The following commands can build and then run the `main.go`.
```bash
# in test_letus
$ go build main.go
$ ./main
```


# Citation