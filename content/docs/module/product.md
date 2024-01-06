---
author: Jay Pipes
date: "2024-01-05T18:39:08-05:00"
description: "Query system BIOS information"
draft: false
icon: quick_reference_all
lastmod: "2024-01-05T18:39:08-05:00"
title: "Product"
toc: true
weight: 611
---

## `ghw.Product()` module function`

The `ghw.Product()` function returns a `ghw.ProductInfo` struct that
contains information about the host computer's hardware product line.

The `ghw.ProductInfo` struct contains multiple fields:

* `ghw.ProductInfo.Family` is a string describing the product family
* `ghw.ProductInfo.Name` is a string with the product name
* `ghw.ProductInfo.SerialNumber` is a string with the product serial number
* `ghw.ProductInfo.UUID` is a string with the product UUID
* `ghw.ProductInfo.SKU` is a string with the product stock unit identifier
  (SKU)
* `ghw.ProductInfo.Vendor` is a string with the product vendor
* `ghw.ProductInfo.Version` is a string with the product version

> **NOTE**: These fields are often missing for non-server hardware. Don't be
> surprised to see empty string, "Default string" or "None" values.

```go
package main

import (
	"context"
	"fmt"

	"github.com/go-hardware/ghw"
)

func main() {
	product, err := ghw.Product(context.TODO())
	if err != nil {
		fmt.Printf("Error getting product info: %v", err)
	}

	fmt.Printf("%v\n", product)
}
```

Example output from my personal workstation:

```
product family=Default string name=Thelio vendor=System76 sku=Default string version=thelio-r1
```

> **NOTE**: Some of the values such as serial numbers are shown as unknown
> because the Linux kernel by default disallows access to those fields if
> you're not running as root.  They will be populated if it runs as root or
> otherwise you may see warnings like the following:

```
WARNING: Unable to read product_serial: open /sys/class/dmi/id/product_serial: permission denied
```

You can ignore them or use the [Disabling warning messages](#disabling-warning-messages)
feature to quiet things down.
