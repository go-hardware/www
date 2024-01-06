---
author: Jay Pipes
date: "2024-01-05T18:39:08-05:00"
description: "Using the ghw command line tool"
draft: false
icon: "rocket_launch"
lastmod: "2024-01-05T18:39:08-05:00"
title: "Using the ghw command line tool"
toc: true
weight: 99
---

The `ghw` command line tool queries system hardware information, prints information in
several formats and can create a tarball snapshot of a Linux system for use in
testing and debugging.

## Install

The `ghw` command line tool may be installed:

* via `go install`
* via release binaries

### via `go install`

For systems with Go installed, the easiest way to install the `ghw` command
line tool is to use `go install`:

```bash
go install github.com/go-hardware/ghw/cmd/ghw
```

This will install the `ghw` in the calling user's `$GOPATH`.

### via release binaries

Visit the `ghw` Github Release Page to see the releases of the `ghw` library.
Each release will include pre-built binary images of the `ghw` command line for
Linux x86, Windows and Darwin.

## Show all host system information

Call `ghw` with no arguments to show all information about the host system's
hardware.

```
$ ghw
block storage (5 disks, 3TB physical storage)
 dm-0 SSD (923GB) Unknown [@unknown (node #0)] vendor=unknown
 dm-1 SSD (923GB) Unknown [@unknown (node #0)] vendor=unknown
 dm-2 SSD (4GB) Unknown [@unknown (node #0)] vendor=unknown
 nvme0n1 SSD (932GB) NVMe [@pci-0000:01:00.0-nvme-1 (node #0)] vendor=unknown model=Samsung SSD 980 PRO 1TB serial=S5P2NS0T336341W WWN=eui.002538b32140a5e8
  nvme0n1p1 (512MB) [vfat] mounted@/boot/efi
  nvme0n1p2 (4GB) [vfat] mounted@/recovery
  nvme0n1p3 (924GB) [crypto_LUKS]
  nvme0n1p4 (4GB) [swap]
 zram0 SSD (16GB) Unknown [@unknown (node #0)] vendor=unknown
cpu (1 physical package, 10 cores, 12 hardware threads)
 physical package #0 (10 cores, 12 hardware threads)
  processor core #0 (2 threads), logical processors [0 1]
  processor core #14 (1 threads), logical processors [10]
  processor core #15 (1 threads), logical processors [11]
  processor core #4 (2 threads), logical processors [2 3]
  processor core #8 (1 threads), logical processors [4]
  processor core #9 (1 threads), logical processors [5]
  processor core #10 (1 threads), logical processors [6]
  processor core #11 (1 threads), logical processors [7]
  processor core #12 (1 threads), logical processors [8]
  processor core #13 (1 threads), logical processors [9]
  capabilities: [msr pae mce cx8 apic sep
                 mtrr pge mca cmov pat pse36
                 clflush dts acpi mmx fxsr sse
                 sse2 ss ht tm pbe syscall
                 nx pdpe1gb rdtscp lm constant_tsc art
                 arch_perfmon pebs bts rep_good nopl xtopology
                 nonstop_tsc cpuid aperfmperf tsc_known_freq pni pclmulqdq
                 dtes64 monitor ds_cpl vmx smx est
                 tm2 ssse3 sdbg fma cx16 xtpr
                 pdcm sse4_1 sse4_2 x2apic movbe popcnt
                 tsc_deadline_timer aes xsave avx f16c rdrand
                 lahf_lm abm 3dnowprefetch cpuid_fault epb ssbd
                 ibrs ibpb stibp ibrs_enhanced tpr_shadow flexpriority
                 ept vpid ept_ad fsgsbase tsc_adjust bmi1
                 avx2 smep bmi2 erms invpcid rdseed
                 adx smap clflushopt clwb intel_pt sha_ni
                 xsaveopt xsavec xgetbv1 xsaves split_lock_detect user_shstk
                 avx_vnni dtherm ida arat pln pts
                 hwp hwp_notify hwp_act_window hwp_epp hwp_pkg_req hfi
                 vnmi umip pku ospke waitpkg gfni
                 vaes vpclmulqdq rdpid movdiri movdir64b fsrm
                 md_clear serialize arch_lbr ibt flush_l1d arch_capabilities]
gpu (1 graphics card)
 card #0 @0000:00:02.0 -> driver: 'i915' class: 'Display controller' vendor: 'Intel Corporation' product: 'unknown'
memory (16GB physical, 16GB usable, 6GB used)
net (1 NICs)
 wlp0s20f3
  enabled capabilities:
   - rx-checksumming
   - tx-checksumming
   - tx-checksum-ipv4
   - tx-checksum-ipv6
   - scatter-gather
   - tx-scatter-gather
   - tcp-segmentation-offload
   - tx-tcp-segmentation
   - tx-tcp6-segmentation
   - generic-segmentation-offload
   - generic-receive-offload
   - highdma
   - netns-local
topology SMP (1 nodes)
 node #0 (10 cores)
  L1i cache (32 KB) shared with logical processors: 0,1
  L1i cache (32 KB) shared with logical processors: 2,3
  L1i cache (64 KB) shared with logical processors: 4
  L1i cache (64 KB) shared with logical processors: 5
  L1i cache (64 KB) shared with logical processors: 6
  L1i cache (64 KB) shared with logical processors: 7
  L1i cache (64 KB) shared with logical processors: 8
  L1i cache (64 KB) shared with logical processors: 9
  L1i cache (64 KB) shared with logical processors: 10
  L1i cache (64 KB) shared with logical processors: 11
  L1d cache (32 KB) shared with logical processors: 0,1
  L1d cache (32 KB) shared with logical processors: 2,3
  L1d cache (64 KB) shared with logical processors: 4
  L1d cache (64 KB) shared with logical processors: 5
  L1d cache (64 KB) shared with logical processors: 6
  L1d cache (64 KB) shared with logical processors: 7
  L1d cache (64 KB) shared with logical processors: 8
  L1d cache (64 KB) shared with logical processors: 9
  L1d cache (64 KB) shared with logical processors: 10
  L1d cache (64 KB) shared with logical processors: 11
  L2 cache (1280 KB) shared with logical processors: 0,1
  L2 cache (1280 KB) shared with logical processors: 2,3
  L2 cache (2048 KB) shared with logical processors: 4,5,6,7
  L2 cache (2048 KB) shared with logical processors: 8,9,10,11
  L3 cache (12288 KB) shared with logical processors: 0,1,2,3,4,5,6,7,8,9,10,11
  memory (16GB physical, 16GB usable)
  distances
    to node #0 10
WARNING: Unable to read chassis_serial: open /sys/class/dmi/id/chassis_serial: permission denied
chassis type=Laptop vendor=System76
bios vendor=coreboot version=2022-11-21_b337ac6 date=11/14/2022
WARNING: Unable to read board_serial: open /sys/class/dmi/id/board_serial: permission denied
baseboard vendor=System76 version=lemp11 product=Lemur Pro
WARNING: Unable to read product_serial: open /sys/class/dmi/id/product_serial: permission denied
WARNING: Unable to read product_uuid: open /sys/class/dmi/id/product_uuid: no such file or directory
product name=Lemur Pro vendor=System76 version=lemp11
```
