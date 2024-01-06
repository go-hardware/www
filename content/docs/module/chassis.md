---
author: Jay Pipes
date: "2024-01-05T18:39:08-05:00"
description: "Query system chassis information"
draft: false
icon: quick_reference_all
lastmod: "2024-01-05T18:39:08-05:00"
title: "Chassis"
toc: true
weight: 608
---

## `ghw.Chassis()` module function

The `ghw.Chassis()` function returns a `ghw.ChassisInfo` struct that contains
information about the host computer's hardware chassis.

The `ghw.ChassisInfo` struct contains multiple fields:

* `ghw.ChassisInfo.AssetTag` is a string with the chassis asset tag
* `ghw.ChassisInfo.SerialNumber` is a string with the chassis serial number
* `ghw.ChassisInfo.Type` is a string with the chassis type *code*
* `ghw.ChassisInfo.TypeDescription` is a string with a description of the
  chassis type
* `ghw.ChassisInfo.Vendor` is a string with the chassis vendor
* `ghw.ChassisInfo.Version` is a string with the chassis version

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
	chassis, err := ghw.Chassis(context.TODO())
	if err != nil {
		fmt.Printf("Error getting chassis info: %v", err)
	}

	fmt.Printf("%v\n", chassis)
}
```

Example output from my personal workstation:

```
chassis type=Desktop vendor=System76 version=thelio-r1
```

> **NOTE**: Some of the values such as serial numbers are shown as unknown
> because the Linux kernel by default disallows access to those fields if
> you're not running as root. They will be populated if it runs as root or
> otherwise you may see warnings like the following:

```
WARNING: Unable to read chassis_serial: open /sys/class/dmi/id/chassis_serial: permission denied
```

You can ignore them or use the [Disabling warning messages](#disabling-warning-messages)
feature to quiet things down.

