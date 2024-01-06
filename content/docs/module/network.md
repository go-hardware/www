---
author: Jay Pipes
date: "2024-01-05T18:39:08-05:00"
description: "Query system network information and usage"
draft: false
icon: quick_reference_all
lastmod: "2024-01-05T18:39:08-05:00"
title: "Network"
toc: true
weight: 605
---

## `ghw.Network()` module function

The `ghw.Network()` function returns a `ghw.NetworkInfo` struct that contains
information about the host computer's networking hardware.

The `ghw.NetworkInfo` struct contains one field:

* `ghw.NetworkInfo.NICs` is an array of pointers to `ghw.NIC` structs, one
  for each network interface controller found for the systen

Each `ghw.NIC` struct contains the following fields:

* `ghw.NIC.Name` is the system's identifier for the NIC
* `ghw.NIC.MACAddress` is the Media Access Control (MAC) address for the NIC,
  if any
* `ghw.NIC.IsVirtual` is a boolean indicating if the NIC is a virtualized
  device
* `ghw.NIC.Capabilities` (Linux only) is an array of pointers to
  `ghw.NICCapability` structs that can describe the things the NIC supports.
  These capabilities match the returned values from the `ethtool -k <DEVICE>`
  call on Linux as well as the AutoNegotiation and PauseFrameUse capabilities
  from `ethtool`.
* `ghw.NIC.PCIAddress` (Linux only) is the PCI device address of the device
  backing the NIC.  this is not-nil only if the backing device is indeed a PCI
  device; more backing devices (e.g. USB) will be added in future versions.
* `ghw.NIC.Speed` (Linux only) is a string showing the current link speed.  On
  Linux, this field will be present even if `ethtool` is not available.
* `ghw.NIC.Duplex` (Linux only) is a string showing the current link duplex. On
  Linux, this field will be present even if `ethtool` is not available.
* `ghw.NIC.SupportedLinkModes` (Linux only) is a string slice containing a list
  of supported link modes, e.g. "10baseT/Half", "1000baseT/Full".
* `ghw.NIC.SupportedPorts` (Linux only) is a string slice containing the list
  of supported port types, e.g. "MII", "TP", "FIBRE", "Twisted Pair".
* `ghw.NIC.SupportedFECModes` (Linux only) is a string slice containing a list
  of supported Forward Error Correction (FEC) Modes.
* `ghw.NIC.AdvertisedLinkModes` (Linux only) is a string slice containing the
  link modes being advertised during auto negotiation.
* `ghw.NIC.AdvertisedFECModes` (Linux only) is a string slice containing the
  Forward Error Correction (FEC) modes advertised during auto negotiation.

The `ghw.NICCapability` struct contains the following fields:

* `ghw.NICCapability.Name` is the string name of the capability (e.g.
  "tcp-segmentation-offload")
* `ghw.NICCapability.IsEnabled` is a boolean indicating whether the capability
  is currently enabled/active on the NIC
* `ghw.NICCapability.CanEnable` is a boolean indicating whether the capability
  may be enabled

```go
package main

import (
	"context"
    "fmt"

    "github.com/go-hardware/ghw"
)

func main() {
    net, err := ghw.Network(context.TODO())
    if err != nil {
        fmt.Printf("Error getting network info: %v", err)
    }

    fmt.Printf("%v\n", net)

    for _, nic := range net.NICs {
        fmt.Printf(" %v\n", nic)

        enabledCaps := make([]int, 0)
        for x, cap := range nic.Capabilities {
            if cap.IsEnabled {
                enabledCaps = append(enabledCaps, x)
            }
        }
        if len(enabledCaps) > 0 {
            fmt.Printf("  enabled capabilities:\n")
            for _, x := range enabledCaps {
                fmt.Printf("   - %s\n", nic.Capabilities[x].Name)
            }
        }
    }
}
```

Example output from my personal laptop:

```
net (3 NICs)
 docker0
  enabled capabilities:
   - tx-checksumming
   - tx-checksum-ip-generic
   - scatter-gather
   - tx-scatter-gather
   - tx-scatter-gather-fraglist
   - tcp-segmentation-offload
   - tx-tcp-segmentation
   - tx-tcp-ecn-segmentation
   - tx-tcp-mangleid-segmentation
   - tx-tcp6-segmentation
   - udp-fragmentation-offload
   - generic-segmentation-offload
   - generic-receive-offload
   - tx-vlan-offload
   - highdma
   - tx-lockless
   - netns-local
   - tx-gso-robust
   - tx-fcoe-segmentation
   - tx-gre-segmentation
   - tx-gre-csum-segmentation
   - tx-ipxip4-segmentation
   - tx-ipxip6-segmentation
   - tx-udp_tnl-segmentation
   - tx-udp_tnl-csum-segmentation
   - tx-gso-partial
   - tx-sctp-segmentation
   - tx-esp-segmentation
   - tx-vlan-stag-hw-insert
 enp58s0f1
  enabled capabilities:
   - rx-checksumming
   - generic-receive-offload
   - rx-vlan-offload
   - tx-vlan-offload
   - highdma
   - auto-negotiation
 wlp59s0
  enabled capabilities:
   - scatter-gather
   - tx-scatter-gather
   - generic-segmentation-offload
   - generic-receive-offload
   - highdma
   - netns-local
```
