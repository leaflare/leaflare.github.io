+++
title = "Reading Notes on umbra"
date = 2022-03-09 20:17:19
slug = "202203092017"

[taxonomies]
tags = ["State-of-the-art DB"]
categories =  ["Reading Notes", "Eng"]

+++

Umbra DB

Umbra: A Disk-Based System with In-Memory Performance (CIDR 2020, TUM)

<!-- more -->

## Basic Info

Umbra is a database designed by the TUM database group after the HyPer in-memory-only database, which is biased towards large memory scenarios but offers better management of supra-physical memory data volumes than the in-memory-only database. Umbra's positioning overlaps with LeanStore and continues many of the LeanStore's designs (e.g. pointer swizzling, page replacement strategy, optimistic latching...) It also continues much of the design of the LeanStore (e.g. pointer swizzling, page replacement strategy, optimistic latching...) but adds variable-size pages to the LeanStore to better support arbitrary data sizes (especially large data objects) without compromising performance. TUM therefore considers Umbra to be the true successor of Hyper (Umbra is the spiritual successor of our pure in-memory system HyPer).

Here are some details on the design of the variable-size page.

## Variable-size Page 

Umbra's page starts at 64KB and is divided into multiple size classes. Theoretically, the page size can be up to the size of the entire buffer pool. Unlike previous variable size page buffer pool designs, Umbra does not need to set the size of the buffer pool memory available for each size class, so it can be used (api) much differently than a normal buffer manager.

However, supporting multiple page sizes can easily lead to fragmentation, and Umbra solves this problem by using a mapping between virtual address and physical memory (although a continuous virtual address may be discrete on physical memory). ). Each virtual memory region is sliced into chunks of the size of that size class, so that at least one of the virtual memory chunks is not allocated. Each virtual memory region is sliced into chunks of that size class, so there is no fragmentation, at least on the virtual memory. When a page is read, a physical memory mapping is created with pread. When a page is evict, a pwrite is written back and the MADV_DONTNEED flag is passed to the madvise system call to immediately release the physical The

Umbra's relation is organised in a B+ tree (using synthetic key), all internal nodes are 64KB pages, and the leaf node can be a variable size page. DataBlock.

In addition to the variable size page, Umbra has made some additional minor optimisations compared to LeanStore, such as shared latching (to reduce the validation failure of optimistic latching in the case of read heavy workload + few writes) failure). Compared to HyPer, changes have been made to string handling (below), statistics collection (online reservoir sampling variant + HyperLogLog variant), and query compilation (pipelines are further disassembled into steps, abandoned the more generic llvm, wrote a more customised lightweight IR of its own, and adapted it for implementation (ICDE2018)).

## Resource

[Umbra](https://umbra-db.com/)
