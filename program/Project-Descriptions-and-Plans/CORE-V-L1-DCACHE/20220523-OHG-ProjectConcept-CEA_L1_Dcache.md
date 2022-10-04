# CORE-V L1 DCACHE - Project Concept Proposal

**Date of proposal**

2022-05-23

Updated 2022-06-27

**Author(s)**

César Fuguet - Research Engineer - CEA List (Grenoble), Adrian Evans -
Research Engineer - CEA List (Grenoble)

Comments and additions by Duncan Bees, Jerome Quevremont

## High Level Summary of project, project components, and deliverables

### Summary

Many applications in the HPC domain (e.g. scientific applications) are
often memory bound: the performance bottleneck is the memory. It is
therefore necessary to implement cache subsystems enabling a high memory
throughput. This is to hide the continuously growing gap between memory
and processor cores speeds (“Memory Wall”).

We propose a Level-1 Data Cache (L1 DCACHE) compatible with RISC-V
processor cores. This L1 DCACHE was successfully integrated with a CVA6
core (replacing its original DCACHE). However, it should be compatible
with other cores (with some modifications in the load/store interface of
this last).

The most important features of the proposed L1 DCACHE are:

  - Hit under multiple misses. Current implementation supports up to 128
    inflight miss requests to the memory. This feature allows benefiting
    of the high throughput in new memory technologies (e.g. High
    Bandwidth Memory or HBM), while hiding the memory latency.

  - Efficient Data RAMs organization to reduce both area and energy
    consumption.

  - High and flexible bandwidth between the cache and the “requester”. A
    requester may be the integer pipeline of the core, or any given
    tightly coupled accelerator.

  - Out-of-order execution of memory operations to avoid unnecessary
    stalls (complying with RISC-V memory consistency models).

  - Cache Management Operations (CMO) like invalidation or fences.

  - Programmable hardware memory prefetcher for stride memory accesses.

### Components

  - L1 DCACHE IP.

  - Hardware Memory Prefetcher IP.

  - UVM testbench for standalone (without core) testing of the L1
    DCACHE.

### Deliverables

  - Documentation of the L1 DCACHE IP

  - Documentation of the Hardware Memory Prefetcher IP.

  - RTL (SystemVerilog) sources of the L1 DCACHE IP.

  - RTL (SystemVerilog) sources of the Hardware Memory Prefetcher IP.

  - UVM (SystemVerilog) testbench for standalone testing of the L1
    DCACHE (in a staged approach).

## Summary of market or input requirements

### Known market/project requirements at PC gate

  - Hardware RISCV-based accelerators in the HPC domain.

  - We (CEA) are already using this L1 DCACHE for our custom RISCV-based
    hardware accelerator: the VRP/VxP (Variable and eXtended precision
    accelerator). This accelerator (using this L1 DCACHE) will be
    taped-out in:
    
      - GlobalFoundaries 22FDX technology in the framework of the
        European Processor Initiative (EPI) EPAC1.5 test-chip (expected
        tape-out on August, 2022).
    
      - TSMC 7nm technology in the framework of the EPI’s RHEA chip
        (2023 Q1).

### Potential future enhancements

Future developments may add features such as

  - Support of one or multiple cache-coherency protocols.

  - Memory error correction (ECC) for complying with constraints in
    critical systems

  - Hybrid write-policy: write-back / write-through.

  - Scratchpad mode (partially or fully). Useful in some embedded
    systems or even HPC for some accelerators.

  - System testing to be determined by other interested parties.

## Who would make use of OpenHW output

The proposed L1 DCACHE should be adaptable to any RISC-V processor core
in HPC domain. Therefore, it could benefit anyone needing a
high-throughput for memory accesses.

## Initial Estimate of Timeline

In order to open-source the L1 DCACHE we would like to accomplish the
following tasks:

  - Adapt newer versions of the CVA6’s memory interface to the L1
    DCACHE. Current port was made on Ariane (version from PULP’s github
    – April 2021). Estimated effort is low: 2 person/month. Estimation
    is based on analysis of latest CVA6 versions.

  - Improve documentation. Estimated effort is low: 1 person/month.

  - Improve the existing UVM verification environment for standalone
    (without core) testing of the L1 DCACHE. Estimated effort is
    medium-high: 6 person/month.

## Explanation of why OpenHW should do this project

  - This L1 DCACHE enables high-performance on memory bound applications
    and it is adaptable to the different RISC-V cores in the OpenHWG
    Cores-V catalog.

  - This project will help to extend the CVA6 ecosystem for next
    generation of CVA6 cores which need more capable cache.

  - First move for OpenHW into HPC

  - Deepen engagement of key EU research organizations (such as CEA,
    BSC) with OpenHW projects

## Industry landscape: description of competing, alternative, or related efforts in the industry

Hereafter some of the features of other open-source, RISC-V compatible,
L1 Data Caches.

<table style="width:94%;">
<colgroup>
<col style="width: 23%" />
<col style="width: 23%" />
<col style="width: 23%" />
<col style="width: 23%" />
</colgroup>
<thead>
<tr class="header">
<th><strong>Features</strong></th>
<th><strong>CVA6 NonBlocking Dcache</strong></th>
<th><strong>Rocket/BOOM NonBlocking Dcache</strong></th>
<th><strong>This project</strong></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><strong>HDL (Hardware Description Language)</strong></td>
<td>SystemVerilog</td>
<td>Chisel (Scala)</td>
<td>SystemVerilog</td>
</tr>
<tr class="even">
<td><strong>MSHR (Miss Status Holding Register)</strong></td>
<td>Implemented in FFs. Up to 1 miss per requester.</td>
<td>Implemented in FFs. Configurable. Up to 32 requests (to stay under 2Kbits).</td>
<td>Implemented in RAM. Up to 128 requests (or more).</td>
</tr>
<tr class="odd">
<td><strong>Hardware Memory Prefetcher</strong></td>
<td>No</td>
<td>No</td>
<td>Yes</td>
</tr>
<tr class="even">
<td><strong>Hardware Cache Coherency</strong></td>
<td>Yes (using OpenPiton)</td>
<td>Yes (using Tilelink)</td>
<td>No. We plan to support hardware cache coherency in a future release.</td>
</tr>
<tr class="odd">
<td><strong>Req Data Width</strong></td>
<td>32, 64</td>
<td>-</td>
<td>Any power of 2, up to 256 bits</td>
</tr>
<tr class="even">
<td><strong>Memory Order</strong></td>
<td>All memory accesses are issued in order. In case of match with MSHR, the requester is stalled.</td>
<td>-</td>
<td>Only memory accesses on the same address are issued in order. In case of match, the request is put in a replay table to be issued later. The requester is not stalled.</td>
</tr>
<tr class="odd">
<td><strong> Write-Policy</strong></td>
<td><p>Write-Back</p>
<p>Write-Through</p></td>
<td>Write-Back</td>
<td>Write-Through</td>
</tr>
</tbody>
</table>

## OpenHW Members/Participants committed to participate

César Fuguet (CEA List, Grenoble) – Designer of the L1 DCACHE.

Adrian Evans (CEA List, Grenoble) – Principal responsible of
verification.

Additional participation for specification and verification plan
reviewing welcomed.

## Project Leader(s)

### Technical Project Leader(s)

César Fuguet (CEA List, Grenoble)

### Project Manager, if a PM is designated

\--

## Next steps/Investigation towards Project Launch (**PC only**)

  - Review of specifications from interested parties and the Cores Task
    Group.

  - Review verification plan. Discuss approaches for verification with
    interested parties and the verification task group. Determine a
    verification phase approach.

  - Meeting with other OpenHWG members/staff to discuss about repository
    structure, simulation tools, CI, compilation/synthesis flow, RTL
    freeze checklist and project board.

### Repository Requirements

  - Separate repository for the L1 DCACHE as it can be used for
    different cores.
