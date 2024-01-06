---
author: Jay Pipes
date: "2024-01-05T18:39:08-05:00"
description: "Query system block storage device capabilities and usage"
draft: false
icon: quick_reference_all
lastmod: "2024-01-05T18:39:08-05:00"
title: "Block storage"
toc: true
weight: 603
---

## `ghw.Block()` module function

The `ghw.Block()` function returns a `ghw.BlockInfo` struct that contains
information about the block storage on the host system.

`ghw.BlockInfo` contains the following fields:

* `ghw.BlockInfo.TotalSizeBytes` contains the amount of physical block storage
  on the host.
* `ghw.BlockInfo.Disks` is an array of pointers to `ghw.Disk` structs, one for
  each disk found by the system

Each `ghw.Disk` struct contains the following fields:

* `ghw.Disk.Name` contains a string with the short name of the disk, e.g. "sda"
* `ghw.Disk.SizeBytes` contains the amount of storage the disk provides
* `ghw.Disk.PhysicalBlockSizeBytes` contains the size of the physical blocks
  used on the disk, in bytes. This is typically the minimum amount of data that
  will be written in a single write operation for the disk.
* `ghw.Disk.IsRemovable` contains a boolean indicating if the disk drive is
  removable
* `ghw.Disk.DriveType` is the type of drive. It is of type `ghw.DriveType`
  which has a `ghw.DriveType.String()` method that can be called to return a
  string representation of the bus. This string will be `HDD`, `FDD`, `ODD`,
  or `SSD`, which correspond to a hard disk drive (rotational), floppy drive,
  optical (CD/DVD) drive and solid-state drive.
* `ghw.Disk.StorageController` is the type of storage controller. It is of type
  `ghw.StorageController` which has a `ghw.StorageController.String()` method
  that can be called to return a string representation of the bus. This string
  will be `SCSI`, `IDE`, `virtio`, `MMC`, or `NVMe`
* `ghw.Disk.BusPath` (Linux, Darwin only) is the filepath to the bus used by
  the disk.
* `ghw.Disk.NUMANodeID` (Linux only) is the numeric index of the NUMA node this
  disk is local to, or -1 if the host system is not a NUMA system or is not
  Linux.
* `ghw.Disk.Vendor` contains a string with the name of the hardware vendor for
  the disk
* `ghw.Disk.Model` contains a string with the vendor-assigned disk model name
* `ghw.Disk.SerialNumber` contains a string with the disk's serial number
* `ghw.Disk.WWN` contains a string with the disk's
  [World Wide Name](https://en.wikipedia.org/wiki/World_Wide_Name)
* `ghw.Disk.Partitions` contains an array of pointers to `ghw.Partition`
  structs, one for each partition on the disk

Each `ghw.Partition` struct contains these fields:

* `ghw.Partition.Name` contains a string with the short name of the partition,
  e.g. `sda1`
* `ghw.Partition.Label` contains the label for the partition itself. On Linux
  systems, this is derived from the `ID_PART_ENTRY_NAME` [udev][udev] entry for
  the partition.
* `ghw.Partition.FilesystemLabel` contains the label for the filesystem housed
  on the partition. On Linux systems, this is derived from the `ID_FS_NAME`
  [udev][udev] entry for the partition.
* `ghw.Partition.SizeBytes` contains the amount of storage the partition
  provides
* `ghw.Partition.MountPoint` contains a string with the partition's mount
  point, or `""` if no mount point was discovered
* `ghw.Partition.Type` contains a string indicated the filesystem type for the
  partition, or `""` if the system could not determine the type
* `ghw.Partition.IsReadOnly` is a bool indicating the partition is read-only
* `ghw.Partition.Disk` is a pointer to the `ghw.Disk` object associated with
  the partition.
* `ghw.Partition.UUID` is a string containing the partition UUID on Linux, the
  partition UUID on MacOS and nothing on Windows. On Linux systems, this is
  derived from the `ID_PART_ENTRY_UUID` [udev][udev] entry for the partition.

[udev]: https://en.wikipedia.org/wiki/Udev

```go
package main

import (
	"context"
	"fmt"

	"github.com/go-hardware/ghw"
)

func main() {
	block, err := ghw.Block(context.TODO())
	if err != nil {
		fmt.Printf("Error getting block storage info: %v", err)
	}

	fmt.Printf("%v\n", block)

	for _, disk := range block.Disks {
		fmt.Printf(" %v\n", disk)
		for _, part := range disk.Partitions {
			fmt.Printf("  %v\n", part)
		}
	}
}
```

Example output from my personal workstation:

```
block storage (1 disk, 2TB physical storage)
 sda HDD (2TB) SCSI [@pci-0000:04:00.0-scsi-0:1:0:0 (node #0)] vendor=LSI model=Logical_Volume serial=600508e000000000f8253aac9a1abd0c WWN=0x600508e000000000f8253aac9a1abd0c
  /dev/sda1 (100MB)
  /dev/sda2 (187GB)
  /dev/sda3 (449MB)
  /dev/sda4 (1KB)
  /dev/sda5 (15GB)
  /dev/sda6 (2TB) [ext4] mounted@/
```

> **NOTE**: `ghw` looks in the udev runtime database for some information. If
> you are using `ghw` in a container, remember to bind mount `/dev/disk` and
> `/run` into your container, otherwise `ghw` won't be able to query the udev
> DB or sysfs paths for information.

