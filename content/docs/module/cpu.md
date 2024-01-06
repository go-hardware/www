---
author: Jay Pipes
date: "2024-01-05T18:39:08-05:00"
description: "Query system CPU capabilities and usage"
draft: false
icon: quick_reference_all
lastmod: "2024-01-05T18:39:08-05:00"
title: "CPU"
toc: true
weight: 601
---

## `ghw.CPU()` module function

The `ghw.CPU()` function returns a `ghw.CPUInfo` struct that contains
information about the CPUs on the host system.

`ghw.CPUInfo` contains the following fields:

* `ghw.CPUInfo.TotalCores` has the total number of physical cores the host
  system contains
* `ghw.CPUInfo.TotalThreads` has the total number of hardware threads the
  host system contains
* `ghw.CPUInfo.Processors` is an array of `ghw.Processor` structs, one for each
  physical processor package contained in the host

Each `ghw.Processor` struct contains a number of fields:

* `ghw.Processor.ID` is the physical processor `uint32` ID according to the
  system
* `ghw.Processor.NumCores` is the number of physical cores in the processor
  package
* `ghw.Processor.NumThreads` is the number of hardware threads in the processor
  package
* `ghw.Processor.Vendor` is a string containing the vendor name
* `ghw.Processor.Model` is a string containing the vendor's model name
* `ghw.Processor.Capabilities` (Linux only) is an array of strings indicating
  the features the processor has enabled
* `ghw.Processor.Cores` (Linux only) is an array of `ghw.ProcessorCore` structs
  that are packed onto this physical processor

A `ghw.ProcessorCore` has the following fields:

* `ghw.ProcessorCore.ID` is the `uint32` identifier that the host gave this
  core. Note that this does *not* necessarily equate to a zero-based index of
  the core within a physical package. For example, the core IDs for an Intel Core
  i7 are 0, 1, 2, 8, 9, and 10
* `ghw.ProcessorCore.NumThreads` is the number of hardware threads associated
  with the core
* `ghw.ProcessorCore.LogicalProcessors` is an array of ints representing the
  logical processor IDs assigned to any processing unit for the core. These are
  sometimes called the "thread siblings". Logical processor IDs are the
  *zero-based* index of the processor on the host and are *not* related to the
  core ID.

```go
package main

import (
	"context"
	"fmt"
	"math"
	"strings"

	"github.com/go-hardware/ghw"
)

func main() {
	cpu, err := ghw.CPU(context.TODO())
	if err != nil {
		fmt.Printf("Error getting CPU info: %v", err)
	}

	fmt.Printf("%v\n", cpu)

	for _, proc := range cpu.Processors {
		fmt.Printf(" %v\n", proc)
		for _, core := range proc.Cores {
			fmt.Printf("  %v\n", core)
		}
		if len(proc.Capabilities) > 0 {
			// pretty-print the (large) block of capability strings into rows
			// of 6 capability strings
			rows := int(math.Ceil(float64(len(proc.Capabilities)) / float64(6)))
			for row := 1; row < rows; row = row + 1 {
				rowStart := (row * 6) - 1
				rowEnd := int(math.Min(float64(rowStart+6), float64(len(proc.Capabilities))))
				rowElems := proc.Capabilities[rowStart:rowEnd]
				capStr := strings.Join(rowElems, " ")
				if row == 1 {
					fmt.Printf("  capabilities: [%s\n", capStr)
				} else if rowEnd < len(proc.Capabilities) {
					fmt.Printf("                 %s\n", capStr)
				} else {
					fmt.Printf("                 %s]\n", capStr)
				}
			}
		}
	}
}
```

Example output from my personal workstation:

```
cpu (1 physical package, 6 cores, 12 hardware threads)
 physical package #0 (6 cores, 12 hardware threads)
  processor core #0 (2 threads), logical processors [0 6]
  processor core #1 (2 threads), logical processors [1 7]
  processor core #2 (2 threads), logical processors [2 8]
  processor core #3 (2 threads), logical processors [3 9]
  processor core #4 (2 threads), logical processors [4 10]
  processor core #5 (2 threads), logical processors [5 11]
  capabilities: [msr pae mce cx8 apic sep
                 mtrr pge mca cmov pat pse36
                 clflush dts acpi mmx fxsr sse
                 sse2 ss ht tm pbe syscall
                 nx pdpe1gb rdtscp lm constant_tsc arch_perfmon
                 pebs bts rep_good nopl xtopology nonstop_tsc
                 cpuid aperfmperf pni pclmulqdq dtes64 monitor
                 ds_cpl vmx est tm2 ssse3 cx16
                 xtpr pdcm pcid sse4_1 sse4_2 popcnt
                 aes lahf_lm pti retpoline tpr_shadow vnmi
                 flexpriority ept vpid dtherm ida arat]
```

