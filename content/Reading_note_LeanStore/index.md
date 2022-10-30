+++
title = "Reading Notes on LeanStore"
date = 2022-03-13 20:17:19
slug = "202203132017"

[taxonomies]
tags = ["State-of-the-art DB"]
categories =  ["Reading Notes", "Eng"]

+++

LeanStore: In-Memory Data Management Beyond Main Memory (ICDE 2018, TUM)

<!-- more -->

LeanStore is a database designed by the TUM database group after HyPer's pure in-memory database, which favours large in-memory scenarios but provides better management of ultra-physical in-memory data volumes than a pure in-memory database.

## Questions

Why go "backwards" from the memory-only databases of the last few years to databases using hard disks? It's really a question of price/performance, 20 years ago when memory-only databases were being talked about, memory capacity was soaring and prices were plummeting, but that trend, like CPUs, has almost stalled in the last 10 years. Memory prices today are only half as cheap as they were a decade ago, but hard drives or SSDs are dropping/increasing in price at a rate that is visible to the naked eye. Mainstream SSDs can now achieve a bandwidth gap that is only an order of magnitude behind memory (a few GB/s vs. tens of GB/s). Also databases actually have a lot of cold data in them, which would be really wasteful to keep in memory as well. But previous in-memory-only databases have always had limitations or severe performance degradation when it comes to external storage, or mechanisms that were too complex to move from the lab to production systems (such as the Anti-Caching design of H-Store, whose indexes contain hot and cold data and the indexes themselves have to be in memory, and given that index sizes often take up half the H-Store often encounters significant memory pressure). For this reason, TUM has designed LeanStore to solve these problems.

## Design goals

1. Performs as well as an in-memory-only database when the amount of data is less than physical memory
2. Better than in-memory databases when the amount of data is greater than physical memory
3. Better than traditional databases in any scenario (using a typical BufferManager, such as BerkeleyDB)

In order to achieve these benefits, LeanStore has adopted the following design：

## Design

1. In a pure memory scenario, using a traditional BufferManager would often result in severe overhead and critical path bottlenecks (e.g. querying the hash table to find the in memory pointer corresponding to the page identifier, which is is an overhead because what's the point of mapping when it's pure memory). As a result, almost all in-memory databases (e.g., HyPer, HANA, H-Store, Hekaton, and Silo) have abandoned BufferManager. However, BufferManager still has many benefits for traditional hard disk databases, such as the ease of managing the amount of data that exceeds memory and the reduction of disk I/O, etc. To facilitate the management of out-of-memory data, LeanStore still uses BufferManager, but with a number of optimisations to reduce the overhead in purely in-memory scenarios.
2. A more lightweight page replacement mechanism has been designed to get cold data out of memory when necessary. Traditional disk-based database page replacement policies (such as LRU and Clock) need to keep track of the number of accesses to each page, etc., which can sometimes become a bottleneck when updating access data for some frequently accessed pages. If you still use these disk-based db policies, you will inevitably be slower than a memory-only database in a memory-only scenario.
3. In terms of concurrency control, a similar approach to optimistic locking is used, an example of which is the Optimistic Lock Coupling on the Adaptive Radix Tree mentioned in the article.

## Resource

V. Leis, M. Haubenschild, A. Kemper and T. Neumann, "LeanStore: In-Memory Data Management beyond Main Memory," 2018 IEEE 34th International Conference on Data Engineering (ICDE), 2018, pp. 185-196, doi: 10.1109/ICDE.2018.00026.
