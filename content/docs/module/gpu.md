---
author: Jay Pipes
date: "2024-01-05T18:39:08-05:00"
description: "Query system GPU information"
draft: false
icon: quick_reference_all
lastmod: "2024-01-05T18:39:08-05:00"
title: "GPU"
toc: true
weight: 607
---

## `ghw.GPU()` module function

The `ghw.GPU()` function returns a `ghw.GPUInfo` struct that contains
information about the host computer's graphics hardware.

The `ghw.GPUInfo` struct contains one field:

* `ghw.GPUInfo.GraphicCards` is an array of pointers to `ghw.GraphicsCard`
  structs, one for each graphics card found for the systen

Each `ghw.GraphicsCard` struct contains the following fields:

* `ghw.GraphicsCard.Index` is the system's numeric zero-based index for the
  card on the bus
* `ghw.GraphicsCard.Address` is the PCI address for the graphics card
* `ghw.GraphicsCard.DeviceInfo` is a pointer to a `ghw.PCIDevice` struct
  describing the graphics card. This may be `nil` if no PCI device information
  could be determined for the card.
* `ghw.GraphicsCard.Node` is an pointer to a `ghw.TopologyNode` struct that the
  GPU/graphics card is affined to. On non-NUMA systems, this will always be
  `nil`.

```go
package main

import (
	"context"
	"fmt"

	"github.com/go-hardware/ghw"
)

func main() {
	gpu, err := ghw.GPU(context.TODO())
	if err != nil {
		fmt.Printf("Error getting GPU info: %v", err)
	}

	fmt.Printf("%v\n", gpu)

	for _, card := range gpu.GraphicsCards {
		fmt.Printf(" %v\n", card)
	}
}
```

Example output from my personal workstation:

```
gpu (1 graphics card)
 card #0 @0000:03:00.0 -> class: 'Display controller' vendor: 'NVIDIA Corporation' product: 'GP107 [GeForce GTX 1050 Ti]'
```

**NOTE**: You can [read more](#pci) about the fields of the `ghw.PCIDevice`
struct if you'd like to dig deeper into PCI subsystem and programming interface
information

**NOTE**: You can [read more](#topology) about the fields of the
`ghw.TopologyNode` struct if you'd like to dig deeper into the NUMA/topology
subsystem
