# Bluge Segment API

[![PkgGoDev](https://pkg.go.dev/badge/github.com/vcaesar/bluge_segment_api)](https://pkg.go.dev/github.com/vcaesar/bluge_segment_api)
[![Tests](https://github.com/vcaesar/bluge_segment_api/actions/workflows/tests.yml/badge.svg?branch=master&event=push)](https://github.com/vcaesar/bluge_segment_api/actions/workflows/tests.yml)
[![Lint](https://github.com/vcaesar/bluge_segment_api/actions/workflows/lint.yml/badge.svg?branch=master&event=push)](https://github.com/vcaesar/bluge_segment_api/actions/workflows/lint.yml)

These interfaces define the relationship between Bluge and it's segment file formats.

The expectation is that these interfaces can be well-defined, and evolve slowly. By doing so, Bluge and the file formats themselves can evolve more quickly, and yet remain compatible.

## Demo

This package provides segment interfaces and data helpers, not a complete search engine. The following example reads a byte range and streams a slice using the in-memory `Data` helper.

Save it as `main.go` in a separate Go module, then run:

```sh
go get github.com/vcaesar/bluge_segment_api
go run .
```

```go
package main

import (
	"fmt"
	"io"
	"log"

	segment "github.com/vcaesar/bluge_segment_api"
)

func main() {
	data := segment.NewDataBytes([]byte("hello bluge"))

	word, err := data.Read(6, data.Len())
	if err != nil {
		log.Fatal(err)
	}
	fmt.Println(string(word))

	greeting := data.Slice(0, 5)
	contents, err := io.ReadAll(greeting.Reader())
	if err != nil {
		log.Fatal(err)
	}
	fmt.Println(string(contents))
}
```

Output:

```text
bluge
hello
```

`Read` and `Slice` use start-inclusive, end-exclusive bounds. Keep bounds within `Data.Len()`. Memory-backed data shares the original byte slice; copy returned bytes if you need independent storage.
