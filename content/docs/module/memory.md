---
author: Jay Pipes
date: "2024-01-05T18:39:08-05:00"
description: "Query system memory information and usage"
draft: false
icon: quick_reference_all
lastmod: "2024-01-05T18:39:08-05:00"
title: "Memory"
toc: true
weight: 602
---

## `ghw.Memory()` module function

The `ghw.Memory()` function returns a `ghw.MemoryInfo` struct that contains
information about the RAM on the host system.

`ghw.MemoryInfo` contains the following fields:

* `ghw.MemoryInfo.TotalPhysicalBytes` contains the amount of physical memory on
  the host
* `ghw.MemoryInfo.TotalUsableBytes` contains the amount of memory the
  system can actually use. Usable memory accounts for things like the kernel's
  resident memory size and some reserved system bits. Please note this value is
  **NOT** the amount of memory currently in use by processes in the system. See
  [the discussion][#physical-versus-usage-memory] about the difference.
* `ghw.MemoryInfo.TotalUsedBytes` contains the amount of memory the system is
  currently using. On Linux, this value is calculated by subtracting the sum of
  free, buffered, cached and slab reclaimable memory from the total amount of
  usable memory.
* `ghw.MemoryInfo.SupportedPageSizes` is an array of integers representing the
  size, in bytes, of memory pages the system supports
* `ghw.MemoryInfo.Modules` is an array of pointers to `ghw.MemoryModule`
  structs, one for each physical [DIMM](https://en.wikipedia.org/wiki/DIMM).
  Currently, this information is only included on Windows, with Linux support
  [planned](https://github.com/go-hardware/ghw/pull/171#issuecomment-597082409).

```go
package main

import (
	"context"
	"fmt"

	"github.com/go-hardware/ghw"
)

func main() {
	memory, err := ghw.Memory(context.TODO())
	if err != nil {
		fmt.Printf("Error getting memory info: %v", err)
	}

	fmt.Println(memory.String())
}
```

Example output from my personal workstation:

```
memory (24GB physical, 24GB usable, 9GB used)
```

## Physical versus Usable Memory

There has been [some](https://github.com/go-hardware/ghw/pull/171)
[confusion](https://github.com/go-hardware/ghw/issues/183) regarding the
difference between the total physical bytes versus total usable bytes of
memory.

Some of this confusion has been due to a misunderstanding of the term "usable".
As mentioned [above](#inspection!=monitoring), `ghw` does inspection of the
system's capacity.

A host computer has two capacities when it comes to RAM. The first capacity is
the amount of RAM that is contained in all memory banks (DIMMs) that are
attached to the motherboard. `ghw.MemoryInfo.TotalPhysicalBytes` refers to this
first capacity.

There is a (usually small) amount of RAM that is consumed by the bootloader
before the operating system is started (booted). Once the bootloader has booted
the operating system, the amount of RAM that may be used by the operating
system and its applications is fixed. `ghw.MemoryInfo.TotalUsableBytes` refers
to this second capacity.

You can determine the amount of RAM that the bootloader used (that is not made
available to the operating system) by subtracting
`ghw.MemoryInfo.TotalUsableBytes` from `ghw.MemoryInfo.TotalPhysicalBytes`:

```go
package main

import (
	"context"
	"fmt"

	"github.com/go-hardware/ghw"
)

func main() {
	memory, err := ghw.Memory(context.TODO())
	if err != nil {
		fmt.Printf("Error getting memory info: %v", err)
	}

        phys := memory.TotalPhysicalBytes
        usable := memory.TotalUsableBytes

	fmt.Printf("The bootloader consumes %d bytes of RAM\n", phys - usable)
}
```

Example output from my personal workstation booted into a Windows10 operating
system with a Linux GRUB bootloader:

```
The bootloader consumes 3832720 bytes of RAM
```
