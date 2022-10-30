+++
title = "Reading Notes on Anti-Caching"
date = 2022-04-02 20:17:19
slug = "202204022017"

[taxonomies]
tags = ["State-of-the-art DB"]
categories =  ["Reading Notes", "Eng"]

+++

Anti-Caching: A New Approach to Database Management System Architecture (VLDB 2013, Brown + MIT)

<!-- more -->

Anti-Caching is a data management mechanism designed by the H-Store project team for all-memory databases and is expected to solve the problem of physical memory limitations.

## Questions

The reason for the redesign is that the previous disk-based BufferManager had an excessive overhead in full memory scenarios (if the page is already in main memory, finding the corresponding memory pointer based on the page identifier is completely useless, according to a According to a SIGMOD 2018 paper, 1/3 of CPU cycles are wasted here). For this reason, many pure memory databases have abandoned BufferManager (e.g. H-Store, VoltDB [the commercial version of H-Store], MemSQL, and RAMCloud). The problem was that when the amount of physical memory was exceeded, pure in-memory databases started to experience page faults, which suddenly and severely slowed down the system. So at the time, pure memory databases were advising users not to manage more data than was available in physical memory. To solve this problem, the H-Store project team designed Anti-Caching.

## Design goals

When physical memory is about to be exhausted, the database collects cold data and writes it to disk in a cost effective manner.

Unlike previous cache mechanisms, data does not exist in both hard disk and memory, but only in one of these places. Because memory is the primary carrier of data, the typical scenario is that data starts in memory and then cold data is expelled to the hard disk (rather than data starting on the hard disk and hot data being cached to memory). This approach is actually somewhat similar to virtual memory, except that memory is now managed by the database rather than the operating system, which provides more precise control (e.g., tuple level eviction).

To understand the design of Anti-Caching it may be useful to review the key features of the H-Stored pure memory database: the H-Store is single-transaction (multiple physical nodes, multiple partitions per node, one thread per partition responsible for executing queries involving that partition), so it is lock-free. This is fine when the query mostly involves only one partition), so there is no locking. In terms of persistence, snapshots are flushed periodically, and command logs are also flushed (irregularly, or less than a max period, I guess) (after the group, to reduce the number of I/Os), and only after the command log has been dropped does the commit return successful.

## Design

The core operation for implementing Anti-Caching is a two-step process.

Aggregate the tuple of cold data to be expelled from memory (according to the LRU) into a block.
Update an in-memory table (evicted table) to keep track of which tuples have been written to disk.
And, because H-Stroe is single-transaction, maintaining these data structures does not require chaining.

All that remains to be solved is some auxiliary data structures and how to do this elegantly. For example, in addition to the evicted table, there will be a block table to maintain all blocks. To facilitate access to cold data, a LRU Chain is maintained (for each table), so that when a tuple is accessed, it is retrieved directly from its original location and placed at the end of the chain. When implemented, the pointer to this bi-linked table is placed directly in the tuple header to reduce memory usage. To reduce CPU pressure, only some transactions are selected to update the LRU information (sampling). In addition to the data itself, the block table and evicted table are also written to disk during persistence.

## Conclusions

Question: Why do we need block tables?
My understanding is that if only evicted tables were used, then each tuple would have to hold specific block location information, which would undoubtedly increase the memory footprint and undermine the design goal of expelling tuples to reduce memory pressure. Using additional block tables in a dictionary-like fashion can further compress memory usage.

Another trade-off is that the tuple from the block has to be merged back into the in-memory table structure, but should it be just this data? Or should the whole block be merged? If the former, the blocks on disk will need to be tidied up regularly, otherwise there will be more and more holes. If the latter, it may increase the memory load and the number of evicts.

The experiments were compared with MySQL, MySQL + Memcached, using the YCSB (Yahoo Cloud Serving Benchmark) and TPC-C (OLTP) datasets. The basic conclusion is that the more skewed the query the more pronounced the Anti-Caching effect. After all, most of the hot pages used can be kept in memory at this point.

## Resource

Justin DeBrabant, Andrew Pavlo, Stephen Tu, Michael Stonebraker, and Stan Zdonik. 2013. Anti-caching: a new approach to database management system architecture. Proc. VLDB Endow. 6, 14 (September 2013), 1942–1953. https://doi.org/10.14778/2556549.2556575
