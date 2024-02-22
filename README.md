
# lstopo Guide

## Introduction
`lstopo` (Logical TOPOlogy of the System) is a tool provided by the `hwloc` (Hardware Locality) library, which aims to map the hardware topology of the system, including NUMA nodes, CPUs, memory, caches, and more. This document covers how to install `lstopo`, use it, and understand its output.

## Installation

### On Debian/Ubuntu:

```bash
sudo apt-get update
sudo apt-get install hwloc
```

### On Red Hat/CentOS:

```bash
sudo yum install hwloc
```

### On Fedora:

```bash
sudo dnf install hwloc
```

## Usage

To run `lstopo` and generate a graphical overview of your system's topology, simply type:

```bash
lstopo
```

For a text-based output, which is more suitable for remote sessions or quick checks:

```bash
lstopo-no-graphics
```

## Understanding `lstopo` Output

The output of `lstopo` provides detailed information about the hardware components of your system. Here's a brief overview of what you might see:

- **NUMANode**: Non-Uniform Memory Access node. Indicates memory zones with direct CPU access.
- **Package**: A physical CPU socket.
- **Core**: A physical core within a CPU package.
- **PU**: Processing Unit, also known as a logical core. In systems with hyper-threading, each physical core might be represented by multiple PUs.

### Example

```
Package L#0
  NUMANode L#0 (P#0 251GB)
  L3 L#0 (42MB)
    L2 L#0 (1280KB) + L1d L#0 (48KB) + L1i L#0 (32KB) + Core L#0
      PU L#0 (P#0)
      PU L#1 (P#40)
```

This snippet indicates that CPU 0 (Package L#0) has a NUMA node with 251GB of memory available. It has an L3 cache of 42MB shared across its cores. The first core (Core L#0) has an L2 cache of 1280KB, and L1 caches of 48KB (data) and 32KB (instruction). This core has two logical cores (or processing units), numbered 0 and 40, indicating hyper-threading is enabled.

## CPU 0 Logical Cores 0 and 40

In the example above, CPU 0's first physical core is represented by two logical cores, 0 and 40. This means the physical core is capable of running two threads simultaneously, effectively doubling the parallel processing capability of the core.

## Identifying NUMA Node for Network Interface Cards (NICs)

In the `lstopo` output, Network Interface Cards (NICs) are also listed, showing their connections relative to the system's hardware topology. This information is crucial for optimizing network performance, especially for applications sensitive to latency and bandwidth.

### Example Section Including NICs

```
HostBridge
  PCIBridge
    PCI 18:00.0 (Ethernet)
      Net "ens1f0"
    PCI 18:00.1 (Ethernet)
      Net "ens1f1"
```

This snippet indicates that there are two Ethernet devices, `ens1f0` and `ens1f1`, connected via PCI bridge to the system. The PCI address (e.g., 18:00.0) helps identify the physical location of the NICs in the system's hardware topology.

### Determining NUMA Node Assignment

The placement of the NICs in the `lstopo` output, relative to the NUMA nodes and packages, can give hints about their NUMA node assignment. Optimally, processes utilizing these NICs should run on cores within the same NUMA node to minimize cross-NUMA memory access latency.

For precise optimization, knowing which NUMA node a NIC is connected to allows for configuring applications to use cores and memory from the same NUMA node, enhancing performance by ensuring local memory access and reducing latency.
