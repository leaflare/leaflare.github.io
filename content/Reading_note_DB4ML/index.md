+++
title = "Reading Notes on DB4ML"
date = 2022-02-11 20:17:19
slug = "202202112017"

[taxonomies]
tags = ["DB4ML" ]
categories =  ["Reading Notes", "Eng"]

+++

Reading notes on  https://doi.org/10.1145/3318464.3380575

DB4ML - An In-Memory Database Kernel with Machine Learning Support

<!-- more -->

## ABSTRACT

Authors revisit the question of how  ML algorithms can be best integrated into existing DBMSs to not only avoid expensive data copies to external ML tools but also to comply with regulatory reasons. The key observation is that database transactions already provide an execution model that allows DBMSs to efficiently mimic the execution model of modern parallel ML algorithms. This paper presents DB4ML, an in-memory database kernel that allows applications to implement user-defined ML algorithms and efficiently run them inside a DBMS. Thereby, the ML algorithms are implemented using a programming model based on the idea of so called iterative transactions.

## INTRODUCTION

The standard approach for applying machine learning (ML) algorithms on relational data is to first select the relevant entries with an SQL query and export them from the database into an external ML tool. Then the ML algorithm is run over the extracted data — outside the DBMS — using statistical software packages or ML libraries. However, this approach can impose a high overhead due to expensive data transfers which can significantly slow down the overall learning procedure especially if the datasets are large. Integrating ML algorithms into DBMSs is thus an ongoing effort in both academia and industry. But performance is not the only reason why vendors integrate ML into a DBMS. Another major reason is compliance  that often discourage applications to export any data out of a DBMS since DBMSs already provide rich security frameworks to protect the data from unauthorized access.

Their approach is based on the key observation that modern in-memory database systems already support fine-grained concurrency control in the form of transactions. Yet, traditional transaction execution schemes are often too heavyweight. Consequently, we show how it is possible to efficiently leverage transaction semantics also for ML algorithms. As a main contribution, we present a new in-memory database kernel called DB4ML that is based on transactions but adds extensions to enable ML algorithms on top of classical transaction processing.

They propose the concept of iterative transactions. With iterative transactions the very same transaction can be re-executed multiple times until convergence without the need to be actively re-scheduled for every iteration by a driver program (i.e., a client) as done in existing approaches. Furthermore, as a second extension, they add new isolation levels for machine-learning into DB4ML.

To summarize, the contributions of this paper are:

- They define a programming model for user-defined iterative transactions that supports a wide class of ML algorithms and allows developers to easily integrate new ML algorithms into a DBMS. 
- They discuss the implementation of our transactional database kernel called DB4ML including a storage manager and execution engine that can efficiently run parallel ML algorithms. 
- They showcase through two use cases (PageRank as well as Stochastic Gradient Descent) how ML algorithms could be implemented inside DB4ML. 
- Their experimental evaluation shows for the aforementioned use cases that DB4ML can support ML algorithms with the efficiency of modern specialized ML engines without the need to transfer data out of the DBMS.

## CONCLUSION 

DB4ML offers a new programming model and an execution engine with isolation levels that provide the concurrency schemes required by modern parallel ML algorithms. A central aspect of DB4ML is that the programmer can implement user-defined ML algorithms in DB4ML without having to worry about the low-level details of synchronization. That way, the implementation will automatically benefit from all the parallelization and architectural optimizations which DB4ML contains - as to be expected from a database system. 
