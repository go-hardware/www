---
author: Jay Pipes
date: "2024-01-05T18:39:08-05:00"
description: "Query system baseboard information"
draft: false
icon: quick_reference_all
lastmod: "2024-01-05T18:39:08-05:00"
title: "Baseboard"
toc: true
weight: 610
---

## `ghw.Baseboard()` module function

The `ghw.Baseboard()` function returns a `ghw.BaseboardInfo` struct that
contains information about the host computer's hardware baseboard.

The `ghw.BaseboardInfo` struct contains multiple fields:

* `ghw.BaseboardInfo.AssetTag` is a string with the baseboard asset tag
* `ghw.BaseboardInfo.SerialNumber` is a string with the baseboard serial number
* `ghw.BaseboardInfo.Vendor` is a string with the baseboard vendor
* `ghw.BaseboardInfo.Product` is a string with the baseboard name on Linux and
  Product on Windows
* `ghw.BaseboardInfo.Version` is a string with the baseboard version

> **NOTE**: These fields are often missing for non-server hardware. Don't be
> surprised to see empty string or "None" values.

```go
package main

import (
	"context"
	"fmt"

	"github.com/go-hardware/ghw"
)

func main() {
	baseboard, err := ghw.Baseboard(context.TODO())
	if err != nil {
		fmt.Printf("Error getting baseboard info: %v", err)
	}

	fmt.Printf("%v\n", baseboard)
}
```

Example output from my personal workstation:

```
baseboard vendor=System76 version=thelio-r1
```

> **NOTE**: Some of the values such as serial numbers are shown as unknown
> because the Linux kernel by default disallows access to those fields if
> you're not running as root. They will be populated if it runs as root or
> otherwise you may see warnings like the following:

```
WARNING: Unable to read board_serial: open /sys/class/dmi/id/board_serial: permission denied
```

You can ignore them or use the [Disabling warning messages](#disabling-warning-messages)
feature to quiet things down.
