---
author: Jay Pipes
date: "2024-01-05T18:39:08-05:00"
description: "Query system BIOS information"
draft: false
icon: quick_reference_all
lastmod: "2024-01-05T18:39:08-05:00"
title: "BIOS"
toc: true
weight: 609
---

## `ghw.BIOS()` module function

The `ghw.BIOS()` function returns a `ghw.BIOSInfo` struct that contains
information about the host computer's basis input/output system (BIOS).

The `ghw.BIOSInfo` struct contains multiple fields:

* `ghw.BIOSInfo.Vendor` is a string with the BIOS vendor
* `ghw.BIOSInfo.Version` is a string with the BIOS version
* `ghw.BIOSInfo.Date` is a string with the date the BIOS was flashed/created

```go
package main

import (
	"context"
	"fmt"

	"github.com/go-hardware/ghw"
)

func main() {
	bios, err := ghw.BIOS(context.TODO())
	if err != nil {
		fmt.Printf("Error getting BIOS info: %v", err)
	}

	fmt.Printf("%v\n", bios)
}
```

Example output from my personal workstation:

```
bios vendor=System76 version=F2 Z5 date=11/14/2018
```
