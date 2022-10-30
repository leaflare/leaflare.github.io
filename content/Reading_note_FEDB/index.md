+++
title = "Reading Notes on FEDB"
date = 2022-06-07 20:17:19
slug = "202206072017"

[taxonomies]
tags = ["State-of-the-art DB"]
categories =  ["Reading Notes", "Eng"]

+++

Optimizing In-memory Database Engine for AI-powered On-line Decision Augmentation Using Persistent Memory (VLDB 2021, 4paradigm, NUS, Intel)

<!-- more -->

FEDB is an open source in memory feature database from Fourth Paradigm.

## Questions

Why do we need a special feature database?

The main reason mentioned in the paper is to solve the problem of slow online feature extraction (more on that below). This allows the feature designer to focus on the product level requirements (e.g. low latency, high concurrency, disaster recovery, high availability, scalability, smooth upgrades, monitorability, etc.) and use SQL for operations. This allows feature designers to focus on design and not be distracted by engineering efficiency.

The first thing we need to understand is what online feature extraction is, using the fraud detection example they gave: every time a credit card is swiped, it has to be determined within 10ms whether it is fraud or not. There are a few basic features (a few hundred), but there are many more real-time features (a few thousand) that can only be retrieved by querying the top three shops visited in the period before the card is swiped, and the most frequent purchases, etc. This "time before" is called a time window, and we can set the width of the window to a few minutes, hours, days, etc. The more time windows we have, the more features we can get, and the more accurate the fraud detection will be. The more time windows we have, the more features we get and the more accurate the fraud detection will be. As you can see, the acquisition of these features is indeed time consuming, and if the online application is time sensitive, the feature extraction time may be unacceptable. And this OLDA model of multiple summary queries after a single insert does not fit well with the typical workload of OLTP, OLAP, and time series databases.

## Design 

They have designed a SQL-like language called FEQL (Feature Extraction Query Language) specifically for feature extraction, which allows the user to define multiple time windows at once without having to execute them multiple times. This EFQL is compiled using llvm for parsing optimisation. The lower storage engine tier is a typical high availability architecture.

As an optimization, FEDB can also use PMEM (persistent memory) to implement a double-tier jump table. For the first problem, the authors use PMDK and libpmemobj-cpp provided by Intel to request/reclaim physical memory and maintain a pool of requested memory to build the jump table so that performance is not affected. For the second problem, EFDB uses the last 4 empty bits of the pointer to mark if it is dirty/delete, and if it is dirty and you want to read it, you have to flush it first.

## Resource

https://github.com/4paradigm/OpenMLDB

Chen, Cheng, et al. "Optimizing in-memory database engine for AI-powered on-line decision augmentation using persistent memory." *Proceedings of the VLDB Endowment* 14.5 (2021): 799-812.
