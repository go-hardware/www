---
author: Jay Pipes
date: "2024-01-05T18:39:08-05:00"
description: "Query system topology"
draft: false
icon: quick_reference_all
lastmod: "2024-01-05T18:39:08-05:00"
title: "Topology"
toc: true
weight: 604
---

## `ghw.Topology()` module function

> **NOTE**: Topology support is currently Linux-only. Windows support is
> [planned](https://github.com/go-hardware/ghw/issues/166).

The `ghw.Topology()` function returns a `ghw.TopologyInfo` struct that contains
information about the host computer's architecture (NUMA vs. SMP), the host's
NUMA node layout and processor-specific memory caches.

The `ghw.TopologyInfo` struct contains two fields:

* `ghw.TopologyInfo.Architecture` contains an enum with the value `ghw.NUMA` or
  `ghw.SMP` depending on what the topology of the system is
* `ghw.TopologyInfo.Nodes` is an array of pointers to `ghw.TopologyNode`
  structs, one for each topology node (typically physical processor package)
  found by the system

Each `ghw.TopologyNode` struct contains the following fields:

* `ghw.TopologyNode.ID` is the system's `uint32` identifier for the node
* `ghw.TopologyNode.Memory` is a `ghw.MemoryArea` struct describing the memory
  attached to this node.
* `ghw.TopologyNode.Cores` is an array of pointers to `ghw.ProcessorCore` structs that
  are contained in this node
* `ghw.TopologyNode.Caches` is an array of pointers to `ghw.MemoryCache` structs that
  represent the low-level caches associated with processors and cores on the
  system
* `ghw.TopologyNode.Distance` is an array of distances between NUMA nodes as reported
  by the system.

`ghw.MemoryArea` describes a collection of *physical* RAM on the host.

In the simplest and most common case, all system memory fits in a single memory
area. In more complex host systems, like [NUMA systems][numa], many memory
areas may be present in the host system (e.g. one for each NUMA cell).

[numa]: https://en.wikipedia.org/wiki/Non-uniform_memory_access

The `ghw.MemoryArea` struct contains the following fields:

* `ghw.MemoryArea.TotalPhysicalBytes` contains the amount of physical memory
  associated with this memory area.
* `ghw.MemoryArea.TotalUsableBytes` contains the amount of memory of this
  memory area the system can actually use. Usable memory accounts for things
  like the kernel's resident memory size and some reserved system bits. Please
  note this value is **NOT** the amount of memory currently in use by processes
  in the system. See [the discussion][#physical-versus-usage-memory] about
  the difference.

See above in the [CPU](#cpu) section for information about the
`ghw.ProcessorCore` struct and how to use and query it.

Each `ghw.MemoryCache` struct contains the following fields:

* `ghw.MemoryCache.Type` is an enum that contains one of `ghw.DATA`,
  `ghw.INSTRUCTION` or `ghw.UNIFIED` depending on whether the cache stores CPU
  instructions, program data, or both
* `ghw.MemoryCache.Level` is a positive integer indicating how close the cache
  is to the processor. The lower the number, the closer the cache is to the
  processor and the faster the processor can access its contents
* `ghw.MemoryCache.SizeBytes` is an integer containing the number of bytes the
  cache can contain
* `ghw.MemoryCache.LogicalProcessors` is an array of integers representing the
  logical processors that use the cache

```go
package main

import (
	"context"
	"fmt"

	"github.com/go-hardware/ghw"
)

func main() {
	topology, err := ghw.Topology(context.TODO())
	if err != nil {
		fmt.Printf("Error getting topology info: %v", err)
	}

	fmt.Printf("%v\n", topology)

	for _, node := range topology.Nodes {
		fmt.Printf(" %v\n", node)
		for _, cache := range node.Caches {
			fmt.Printf("  %v\n", cache)
		}
	}
}
```

Example output from my personal workstation:

```
topology SMP (1 nodes)
 node #0 (6 cores)
  L1i cache (32 KB) shared with logical processors: 3,9
  L1i cache (32 KB) shared with logical processors: 2,8
  L1i cache (32 KB) shared with logical processors: 11,5
  L1i cache (32 KB) shared with logical processors: 10,4
  L1i cache (32 KB) shared with logical processors: 0,6
  L1i cache (32 KB) shared with logical processors: 1,7
  L1d cache (32 KB) shared with logical processors: 11,5
  L1d cache (32 KB) shared with logical processors: 10,4
  L1d cache (32 KB) shared with logical processors: 3,9
  L1d cache (32 KB) shared with logical processors: 1,7
  L1d cache (32 KB) shared with logical processors: 0,6
  L1d cache (32 KB) shared with logical processors: 2,8
  L2 cache (256 KB) shared with logical processors: 2,8
  L2 cache (256 KB) shared with logical processors: 3,9
  L2 cache (256 KB) shared with logical processors: 0,6
  L2 cache (256 KB) shared with logical processors: 10,4
  L2 cache (256 KB) shared with logical processors: 1,7
  L2 cache (256 KB) shared with logical processors: 11,5
  L3 cache (12288 KB) shared with logical processors: 0,1,10,11,2,3,4,5,6,7,8,9
```

