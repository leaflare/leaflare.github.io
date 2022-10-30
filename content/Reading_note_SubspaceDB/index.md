+++
title = "Reading Notes on SubspaceDB"
date = 2022-02-18 20:17:19
slug = "202202182017"

[taxonomies]
tags = ["DB4ML" ]
categories =  ["Reading Notes", "Eng"]

+++

Reading notes on  https://doi.org/10.1016/j.datak.2019.05.003

SubspaceDB : In-database subspace clustering for analytical query processing

<!-- more -->

## ABSTRACT

High dimensional data analysis within relational database management systems (RDBMS) is challenging because of inadequate support from SQL. Currently, subspace clustering of high dimensional data is implemented either outside DBMS using wrapper code or inside DBMS using SQL User Defined Functions/Aggregates(UDFs/UDAs). However, both these approaches have potential disadvantages from performance, resource usage, and security perspective for voluminous and frequently updated data.

SubspaceDB implements subspace clustering directly within an RDBMS. SubspaceDB can be over 10 times faster as compared to a conventional wrapper-based or SQL UDF approach. 

## INTRODUCTION

Despite the fact that RDBMS still remain the premium type of data management technology in most of the domains, subspace clustering algorithms do not lend themselves to SQL queries.

Four important queries that help in the formation of subspace clusters for in-DBMS analytics: 

(a) Medoid queries, (b) Neighbourhood queries, (c) Partial similarity queries, and (d) Prominence queries. 

The major contributions of this paper are 

1. Formulation of following relational algebraic operators with optimization objectives 

   -  Computation of partial similarity between two tables. 


   -  Finding representative tuples from a table. 


   -  Finding relevant attributes in a table. 


   -  Characterizing neighbourhood of a given tuple from a table 

2. SubspaceDB : A Querying system that facilitates data summarization, dimensionality reduction and subspace clustering of high dimensional data stored within RDBMS, using the proposed operators. 

3. A framework for querying data via SubspaceDB.

Most of the research work on integrating machine learning with DBMS propose to export data to external tools. But moving large volumes of data is inefficient due to I/O overhead, change of file format and network speed. So, large-scale parallel and distributed file systems like Hadoop and Spark are also used extensively for big data analytics. In spite of these successive tools in front of scalable data analytics, this direction presents significant challenges for high dimensional data analysis, real time data access, space management, security, and concurrency control. In order to address the aforementioned issues, authors propose a new strategy for subspace clustering within DBMS. 

## Proposed analytical query model

1. Dataset is structured and stored in RDBMS. 
2. Though the dimensions of data is quite high, the intrinsic dimensions are less. 
3. The frequency of increase in the number of tuples is higher than that of the attributes.

These operators are devised by an optimal integration of selection, generalized projection, rename, union and aggregate operators. 

1. Computation of partial similarity between two tables. 
2. Finding representative tuples from a table. 
3. Finding relevant attributes in a table. 
4. Characterizing neighbourhood of a given tuple from a table

## Realization of subspace clustering within DBMS

Authors present a top down approach for subspace clustering, called projected clustering, which can be applied on a table within RDBMS by integration of the proposed operators in appropriate manner. The advantage is interactive analysis, reduced data movement, efficient query processing due to reduced search space, and above all retrieval of hidden clusters in different subsets of attributes. 

## Conclusion

SubspaceDB is comprised of algebraic relational operators that facilitate abundance of actionable analytics such as representative selection, feature selection, dimensionality reduction, neighbourhood, segmental similarity measure, and above all subspace clustering within DBMS. DBMSs equipped with these capabilities serve to augment DBMS’s machine learning capability in a seamless manner, obviating the need of separate APIs, external analytic systems or data mining tools.
