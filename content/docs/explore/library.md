---
author: Jay Pipes
date: "2024-01-05T18:39:08-05:00"
description: "Using the ghw library"
draft: false
icon: "rocket_launch"
lastmod: "2024-01-05T18:39:08-05:00"
title: "Using the ghw library"
toc: true
weight: 301
---

`ghw` is primarily designed as a Go library that other Go projects can import.

```go
import "github.com/go-hardware/ghw"
```

The `ghw` library has a set of [*module functions*][modules] that return an
`Info` struct about a particular hardware domain (e.g. CPU, Memory, Block
storage, etc).

Each module function has the same signature, accepting one parameter of type
`context.Context` and returning a pointer to an `Info` struct.

An `Info` struct corresponds to the name of the module being queried. For
example, the `ghw.CPU` module's `Info` struct is `ghw.CPUInfo`. The
`ghw.Memory` module's `Info` struct is `ghw.MemoryInfo`, etc.

```go
package main

import (
	"fmt"
	"context"

	"github.com/go-hardware/ghw"
)

func main() {
	mem, err := ghw.Memory(context.TODO())
	if err != nil {
		fmt.Printf("Error getting memory info: %v", err)
	}

	fmt.Println(mem)
}
```

To control `ghw`'s behaviour, when calling a module function pass in a
`context.Context` object returned from `ghw.NewContext()` that has been
modified with a `ghw` [*With function*][with-functions].

Here is an example of using a with function to disable any warnings that `ghw`
might write to `stderr`:

```go
package main

import (
	"fmt"

	"github.com/go-hardware/ghw"
)

func main() {
	ctx := ghw.NewContext(ghw.WithDisableWarnings())
	cpu, err := ghw.CPU(ctx)
	if err != nil {
		fmt.Printf("Error getting CPU info: %v", err)
	}

	fmt.Printf("%v\n", cpu)
}
```

[modules]: ../modules
[with-functions]: ../with-functions
