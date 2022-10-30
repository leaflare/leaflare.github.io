+++
title = "Reading Notes on DB meets DL"
date = 2022-02-01 20:17:19
slug = "202202012017"

[taxonomies]
tags = ["DB4ML", "ML4DB" ]
categories = ["Reading Notes", "Eng"]

+++

Reading notes on https://doi.org/10.1145/3003665.3003669

<!-- more -->

# BACKGROUND

Deep learning has excelled on complex problems in a variety of data-driven research areas. The database community has been working on data-driven applications for many years and should have dominated this wave of deep learning, but this has not been the case.

This paper discusses several issues in the database domain and the deep learning domain, finds that there are many common problems in the two domains, and discusses several research points where the two domains can potentially contribute to each other.

# DATABASES TO DEEP LEARNING

In addition to high-performance computing equipment, operation scheduling and memory management are also important factors affecting the speed of deep learning training.

## Stand-alone Training

###  Operation Scheduling

The training algorithm for deep learning uses mainly linear algebraic related operations. Operation scheduling will first detect the dependencies of data operations and then assign independent operations to different executors. This step will be based on a data flow diagram or dynamic analysis of the sequence of read and write operations. The same type of problem exists when optimizing transaction execution and query plans in databases, and their solutions can be considered for deep learning. In the case of query plans, for example, the database uses a cost model to estimate the query plan. Accordingly, given the computational resources (executor and memory), deep learning can be considered to create a cost model to find a more optimal solution for the subsequent operation scheduling policy.

### Memory Management

Deep learning models are becoming larger and larger, for example VGG models are limited by memory size and cannot be trained on a normal stand-alone machine. This can now be solved using techniques such as model compression and memory swapping between video memory and memory. Memory management is a popular research topic in the database field, involving memory locality, sharding and cache optimisation. The idea of database fault recovery is similar to the discard and recalculate approach, using the technique of logging all database operations, which allows real-time analysis to be done without the need for static data graphs. Other techniques, such as rubbish collection and memory pooling, will also provide some help with memory management for GPUs.

## Distributed Training

Distributed computing is the conventional method for speeding up the training of deep models. A parameter server is used to accept the parameter gradient values calculated by the working nodes and update the corresponding parameters. Currently there are two main types of methods: data parallelism and model parallelism. Data parallelism consists of data sharding and model backup; model parallelism consists of complete data sets and model sharding. The database field has a long history of research into distributed environments, including parallel databases, P2P systems, and cloud computing.

### Communication

It is assumed that the deep model contains a large number of parameters and that communication between nodes becomes a performance bottleneck in the model training system. Furthermore, for larger computing clusters, message synchronisation between nodes becomes very important. Accordingly, efficient communication protocols are important for either single point multi-GPU training or cluster training. Possible research directions: a) Compression of parameters and gradient values for transmission; b) Rational organisation of server structures to reduce the communication burden between nodes, e.g. tree structures; c) Use of more efficient network devices, e.g. RDMA.

### Concurrency and Consistency

Most deep learning systems use threads and locks directly to control concurrency and guarantee consistency requirements, and no other concurrent implementations, such as actor and concurrent threads, are used for the time being. Sequence consistency and event consistency are both used in deep learning systems. Both approaches face the same scaling problem.

### Fault Tolerance

The database system uses logging and checkpointing to achieve a high fault tolerance mechanism. Current deep learning systems rely heavily on checkpoint files for training site recovery. And frequent logging of checkpoints introduces a large overhead. Compared to the strong consistency requirements of database systems, SGD (stochastic gradient descent) allows for a certain degree of inconsistency, so full logging is not necessary. It is an interesting research question how to combine the features of SGD and the system architecture to achieve efficient fault tolerance.

## Optimization Techniques in Existing Systems

![](.\img\system.PNG)

#  DEEP LEARNING TO DATABASES

## Query Interface

In recent years, deep learning has yielded the best results in NLP (natural language processing) and RNN models have been shown to learn structured data. Can RNN models be used to parse natural language to generate the corresponding SQL and to optimise the SQL using existing database methods? Heuristic rules can be used to detect syntax errors in the generated SQL. The challenge with this problem is the lack of a large training dataset.

## Query Plans

Query plan optimization is a classic problem in the database field. Most database systems use complex heuristics and cost models to generate query plans. As long as the parameters in the SQL are in a certain interval, its execution plan does not change. That is, query plans are sensitive to a small range of parameters. Therefore, a query plan model can be trained to learn a set of SQL queries with their corresponding query plans, which can be used to generate query plans for new SQL. More specifically, RNN models can be used to learn SQL query text and metadata to generate tree-structured query plans. Augmented learning may be used for online training, using execution time and memory traces as feedback signals.

## Crowdsourcing and Knowledge Bases

Many crowdsourcing and knowledge-base related applications introduce problems of entity extraction, disambiguation and integration, where these instances may be a row of records in a database, a node in a graph. Based on the success of deep learning in the field of NLP, such problems could be considered for solution using deep learning. For example, we might learn representations of entities and then use the direct similarity calculations of these representations to reason about the relationships between entities.

## Spatial and Temporal Data

Spatial and temporal data are common types in database systems and are often used for trend analysis, process modelling and predictive analysis. If blocks in spatial data are understood as pixel points in a picture, spatial relationships can then be extracted using deep learning models such as CNNs. For example, we can learn real-time location data of moving objects (e.g. GPS) into a CNN model to obtain density relationships in neighbourhoods and predict congestion over time. If temporal data can be modelled as a temporal matrix, deep learning (e.g. RNN) can be designed to analyse temporal dependencies and predict whether something is sent at a future point in time. For example, a temporal model based on the spread of a disease could help doctors predict the severity of a particular disease.
