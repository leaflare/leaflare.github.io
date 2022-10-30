+++
title = "Reading Notes on Gorgon"
date = 2022-02-25 20:17:19
slug = "202202252017"

[taxonomies]
tags = ["DB4ML" , "ML4DB"]
categories =  ["Reading Notes", "Eng"]

+++

Reading notes on https://doi.org/10.1109/ISCA45697.2020.00035

Gorgon: Accelerating Machine Learning from Relational Data

<!-- more -->

## ABSTRACT

One emerging application is in-database machine learning: a high-performance, low-friction interface for analytics on large databases. They co-locate database and machine learning processing in a unified reconfigurable data analytics accelerator, Gorgon, which flexibly shares resources between db and ml without compromising performance or incurring excessive overheads in either domain. They distill and integrate database parallel patterns into an existing ML-focused cgra, increasing area by less than 4% while outperforming a multicore software baseline by 1500X. They also explore the performance impact of unifying db and ml in a single accelerator, showing up to 4x speedup over split accelerators.

## INTRODUCTION

Gorgon is a unified data analytics CGRA for in-database machine learning (DB+ML) with ML-focused parallel patterns (map, reduce, etc.) and DB-focused parallel patterns (sort, join, group-by, etc.). Gorgon thus accelerates full data-processing pipelines and is more reconfigurable than previous CGRAS.

Industrial BD+ML systems, like Google's BigQuery ML and Apaches' MADlib, add ML inference and training to the relational model as new SQL operators. DB and ML have been accelerated individually, but no single hardware platform accelerates both.

The key contribution of this paper:

Detailed microarchitectual and system=level design of low-overhead database primitives embedded in a general-purpose reconfigurable accelerator.

An evaluation of end-to-end system performance, including comparisons to software and independent DB and ML accelerators.

## RELATED WORK

database acceleration ASICs,Q100

FPGA database accelerators

Domain-agnostic dataflow accelerators NoC

In-database Machine Learning DAnA

A variety of software solutions integrate ML with database operations, including Apache's Spark and MADlib. Greenplum Database and Google's BigQuery ML. Spark is an open-source distributed data-processing engine for various workloads, including SQL, machine learning, and graph processing.BigQuery ML can run ML models in its cloud data warehouse. MADlib is an open-source project that provides SQL-based ML algorithms that run in a database.

## CONCLUSION

A unified, performant, and flexible accelerator--the ideal, combining the advantages of fixed-function accelerators and FPGAS--would simplify data center management by keeping compute resources homogeneous. Furthermore, accelerator unification enables new, cross-domain applications like DB+ML. Gorgon demonstrates the potential for reuse between accelerators by embedding database inspired paralled patterns in a general-purpose fabric originally designed for ML; it is also the first system to fuse and accelerate DB+ML processing on a single chip, accelerating full data-processing pipelines. 

Gorgon is better-suited to data center integration than a sea of heterogeneous accelerators and faster. Furthermore, a unified platform shares expensive resources and large dice in advanced nodes. By adding database support to an ML-focused accelerator, Gorgon enables more powerful ML pipelines than existing accelerators.
