---
author: Jay Pipes
date: "2024-01-05T18:39:08-05:00"
description: "Query system PCI information"
draft: false
icon: quick_reference_all
lastmod: "2024-01-05T18:39:08-05:00"
title: "PCI"
toc: true
weight: 606
---

## `ghw.PCI()` module function

`ghw` contains a PCI database inspection and querying facility that allows
developers to not only gather information about devices on a local PCI bus but
also query for information about hardware device classes, vendor and product
information.

> **NOTE**: Parsing of the PCI-IDS file database is provided by the separate
> [github.com/jaypipes/pcidb library](http://github.com/jaypipes/pcidb). You
> can read that library's README for more information about the various structs
> that are exposed on the `ghw.PCIInfo` struct.

The `ghw.PCI()` function returns a `ghw.PCIInfo` struct that contains
information about the host computer's PCI devices.

The `ghw.PCIInfo` struct contains one field:

* `ghw.PCIInfo.Devices` is a slice of pointers to `ghw.PCIDevice` structs that
  describe the PCI devices on the host system

> **NOTE**: PCI products are often referred to by their "device ID". We use the
> term "product ID" in `ghw` because it more accurately reflects what the
> identifier is for: a specific product line produced by the vendor.

The `ghw.PCIDevice` struct has the following fields:

* `ghw.PCIDevice.Vendor` is a pointer to a `pcidb.Vendor` struct that
  describes the device's primary vendor. This will always be non-nil.
* `ghw.PCIDevice.Product` is a pointer to a `pcidb.Product` struct that
  describes the device's primary product. This will always be non-nil.
* `ghw.PCIDevice.Subsystem` is a pointer to a `pcidb.Product` struct that
  describes the device's secondary/sub-product. This will always be non-nil.
* `ghw.PCIDevice.Class` is a pointer to a `pcidb.Class` struct that
  describes the device's class. This will always be non-nil.
* `ghw.PCIDevice.Subclass` is a pointer to a `pcidb.Subclass` struct
  that describes the device's subclass. This will always be non-nil.
* `ghw.PCIDevice.ProgrammingInterface` is a pointer to a
  `pcidb.ProgrammingInterface` struct that describes the device subclass'
  programming interface. This will always be non-nil.
* `ghw.PCIDevice.Driver` is a string representing the device driver the
  system is using to handle this device. Can be empty string if this
  information is not available. If the information is not available, this does
  not mean the device is not functioning, but rather that `ghw` was not able to
  retrieve driver information.

The `ghw.PCIAddress` (which is an alias for the `ghw.pci.address.Address`
struct) contains the PCI address fields. It has a `ghw.PCIAddress.String()`
method that returns the canonical Domain:Bus:Device.Function ([D]BDF)
representation of this Address.

The `ghw.PCIAddress` struct has the following fields:

* `ghw.PCIAddress.Domain` is a string representing the PCI domain component of
  the address.
* `ghw.PCIAddress.Bus` is a string representing the PCI bus component of
  the address.
* `ghw.PCIAddress.Device` is a string representing the PCI device component of
  the address.
* `ghw.PCIAddress.Function` is a string representing the PCI function component of
  the address.

The following code snippet shows how to list the PCI devices on the host system
and output a simple list of PCI address and vendor/product information:

```go
package main

import (
	"context"
	"fmt"

	"github.com/go-hardware/ghw"
)

func main() {
	pci, err := ghw.PCI(context.TODO())
	if err != nil {
		fmt.Printf("Error getting PCI info: %v", err)
	}
	fmt.Printf("host PCI devices:\n")
	fmt.Println("====================================================")

	for _, device := range pci.Devices {
		vendor := device.Vendor
		vendorName := vendor.Name
		if len(vendor.Name) > 20 {
			vendorName = string([]byte(vendorName)[0:17]) + "..."
		}
		product := device.Product
		productName := product.Name
		if len(product.Name) > 40 {
			productName = string([]byte(productName)[0:37]) + "..."
		}
		fmt.Printf("%-12s\t%-20s\t%-40s\n", device.Address, vendorName, productName)
	}
}
```

on my local workstation the output of the above looks like the following:

```
host PCI devices:
====================================================
0000:00:00.0	Intel Corporation   	5520/5500/X58 I/O Hub to ESI Port
0000:00:01.0	Intel Corporation   	5520/5500/X58 I/O Hub PCI Express Roo...
0000:00:02.0	Intel Corporation   	5520/5500/X58 I/O Hub PCI Express Roo...
0000:00:03.0	Intel Corporation   	5520/5500/X58 I/O Hub PCI Express Roo...
0000:00:07.0	Intel Corporation   	5520/5500/X58 I/O Hub PCI Express Roo...
0000:00:10.0	Intel Corporation   	7500/5520/5500/X58 Physical and Link ...
0000:00:10.1	Intel Corporation   	7500/5520/5500/X58 Routing and Protoc...
0000:00:14.0	Intel Corporation   	7500/5520/5500/X58 I/O Hub System Man...
0000:00:14.1	Intel Corporation   	7500/5520/5500/X58 I/O Hub GPIO and S...
0000:00:14.2	Intel Corporation   	7500/5520/5500/X58 I/O Hub Control St...
0000:00:14.3	Intel Corporation   	7500/5520/5500/X58 I/O Hub Throttle R...
0000:00:19.0	Intel Corporation   	82567LF-2 Gigabit Network Connection
0000:00:1a.0	Intel Corporation   	82801JI (ICH10 Family) USB UHCI Contr...
0000:00:1a.1	Intel Corporation   	82801JI (ICH10 Family) USB UHCI Contr...
0000:00:1a.2	Intel Corporation   	82801JI (ICH10 Family) USB UHCI Contr...
0000:00:1a.7	Intel Corporation   	82801JI (ICH10 Family) USB2 EHCI Cont...
0000:00:1b.0	Intel Corporation   	82801JI (ICH10 Family) HD Audio Contr...
0000:00:1c.0	Intel Corporation   	82801JI (ICH10 Family) PCI Express Ro...
0000:00:1c.1	Intel Corporation   	82801JI (ICH10 Family) PCI Express Po...
0000:00:1c.4	Intel Corporation   	82801JI (ICH10 Family) PCI Express Ro...
0000:00:1d.0	Intel Corporation   	82801JI (ICH10 Family) USB UHCI Contr...
0000:00:1d.1	Intel Corporation   	82801JI (ICH10 Family) USB UHCI Contr...
0000:00:1d.2	Intel Corporation   	82801JI (ICH10 Family) USB UHCI Contr...
0000:00:1d.7	Intel Corporation   	82801JI (ICH10 Family) USB2 EHCI Cont...
0000:00:1e.0	Intel Corporation   	82801 PCI Bridge
0000:00:1f.0	Intel Corporation   	82801JIR (ICH10R) LPC Interface Contr...
0000:00:1f.2	Intel Corporation   	82801JI (ICH10 Family) SATA AHCI Cont...
0000:00:1f.3	Intel Corporation   	82801JI (ICH10 Family) SMBus Controller
0000:01:00.0	NEC Corporation     	uPD720200 USB 3.0 Host Controller
0000:02:00.0	Marvell Technolog...	88SE9123 PCIe SATA 6.0 Gb/s controller
0000:02:00.1	Marvell Technolog...	88SE912x IDE Controller
0000:03:00.0	NVIDIA Corporation  	GP107 [GeForce GTX 1050 Ti]
0000:03:00.1	NVIDIA Corporation  	UNKNOWN
0000:04:00.0	LSI Logic / Symbi...	SAS2004 PCI-Express Fusion-MPT SAS-2 ...
0000:06:00.0	Qualcomm Atheros    	AR5418 Wireless Network Adapter [AR50...
0000:08:03.0	LSI Corporation     	FW322/323 [TrueFire] 1394a Controller
0000:3f:00.0	Intel Corporation   	UNKNOWN
0000:3f:00.1	Intel Corporation   	Xeon 5600 Series QuickPath Architectu...
0000:3f:02.0	Intel Corporation   	Xeon 5600 Series QPI Link 0
0000:3f:02.1	Intel Corporation   	Xeon 5600 Series QPI Physical 0
0000:3f:02.2	Intel Corporation   	Xeon 5600 Series Mirror Port Link 0
0000:3f:02.3	Intel Corporation   	Xeon 5600 Series Mirror Port Link 1
0000:3f:03.0	Intel Corporation   	Xeon 5600 Series Integrated Memory Co...
0000:3f:03.1	Intel Corporation   	Xeon 5600 Series Integrated Memory Co...
0000:3f:03.4	Intel Corporation   	Xeon 5600 Series Integrated Memory Co...
0000:3f:04.0	Intel Corporation   	Xeon 5600 Series Integrated Memory Co...
0000:3f:04.1	Intel Corporation   	Xeon 5600 Series Integrated Memory Co...
0000:3f:04.2	Intel Corporation   	Xeon 5600 Series Integrated Memory Co...
0000:3f:04.3	Intel Corporation   	Xeon 5600 Series Integrated Memory Co...
0000:3f:05.0	Intel Corporation   	Xeon 5600 Series Integrated Memory Co...
0000:3f:05.1	Intel Corporation   	Xeon 5600 Series Integrated Memory Co...
0000:3f:05.2	Intel Corporation   	Xeon 5600 Series Integrated Memory Co...
0000:3f:05.3	Intel Corporation   	Xeon 5600 Series Integrated Memory Co...
0000:3f:06.0	Intel Corporation   	Xeon 5600 Series Integrated Memory Co...
0000:3f:06.1	Intel Corporation   	Xeon 5600 Series Integrated Memory Co...
0000:3f:06.2	Intel Corporation   	Xeon 5600 Series Integrated Memory Co...
0000:3f:06.3	Intel Corporation   	Xeon 5600 Series Integrated Memory Co...
```

### Finding a PCI device by PCI address

In addition to the above information, the `ghw.PCIInfo` struct has the
following method:

* `ghw.PCIInfo.GetDevice(address string)`

The following code snippet shows how to call the `ghw.PCIInfo.GetDevice()`
method and use its returned `ghw.PCIDevice` struct pointer:

```go
package main

import (
	"context"
	"fmt"
	"os"

	"github.com/go-hardware/ghw"
)

func main() {
	pci, err := ghw.PCI(context.TODO())
	if err != nil {
		fmt.Printf("Error getting PCI info: %v", err)
	}

	addr := "0000:00:00.0"
	if len(os.Args) == 2 {
		addr = os.Args[1]
	}
	fmt.Printf("PCI device information for %s\n", addr)
	fmt.Println("====================================================")
	deviceInfo := pci.GetDevice(addr)
	if deviceInfo == nil {
		fmt.Printf("could not retrieve PCI device information for %s\n", addr)
		return
	}

	vendor := deviceInfo.Vendor
	fmt.Printf("Vendor: %s [%s]\n", vendor.Name, vendor.ID)
	product := deviceInfo.Product
	fmt.Printf("Product: %s [%s]\n", product.Name, product.ID)
	subsystem := deviceInfo.Subsystem
	subvendor := pci.Vendors[subsystem.VendorID]
	subvendorName := "UNKNOWN"
	if subvendor != nil {
		subvendorName = subvendor.Name
	}
	fmt.Printf("Subsystem: %s [%s] (Subvendor: %s)\n", subsystem.Name, subsystem.ID, subvendorName)
	class := deviceInfo.Class
	fmt.Printf("Class: %s [%s]\n", class.Name, class.ID)
	subclass := deviceInfo.Subclass
	fmt.Printf("Subclass: %s [%s]\n", subclass.Name, subclass.ID)
	progIface := deviceInfo.ProgrammingInterface
	fmt.Printf("Programming Interface: %s [%s]\n", progIface.Name, progIface.ID)
}
```

Here's a sample output from my local workstation:

```
PCI device information for 0000:03:00.0
====================================================
Vendor: NVIDIA Corporation [10de]
Product: GP107 [GeForce GTX 1050 Ti] [1c82]
Subsystem: UNKNOWN [8613] (Subvendor: ASUSTeK Computer Inc.)
Class: Display controller [03]
Subclass: VGA compatible controller [00]
Programming Interface: VGA controller [00]
```
