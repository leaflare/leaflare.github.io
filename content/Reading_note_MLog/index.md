+++
title = "Reading Notes on MLog"
date = 2022-02-08 20:17:19
slug = "202202082017"

[taxonomies]
tags = ["DB4ML" ]
categories =  ["Reading Notes", "Eng"]

+++

Reading notes on  https://doi.org/10.14778/3137765.3137812

MLog: towards declarative in-database machine learning

<!-- more -->

## ABSTRACT

MLOG is a high-level language that integrates machine learning into data management systems. MLog is declarative, in the sense that the system manages all data movement, data persistency, and machine-learning related optimizations (such as data batching) automatically. With MLog, users can succinctly specify not only simple models such as SVM (in just two lines), but also sophisticated deep learning models that are not supported by existing in-database analytics systems (e.g., MADlib, PAL, and SciDB), as a series of cascaded TViews.

## INTRODUCTION

In this paper, authors demonstrate MLOG, a system that aims for marrying Keras-like declarative machine learning to SciDB-like declarative data management. In MLOG, they build upon a standard data model similar to SciDB, to avoid neglecting and reinventing decades of study of data management. Their approach is to extend the query language over the SciDB data model to allow users to specify machine learning models in a way similar to traditional relational views and relational queries. Specifically, they demonstrate the following three main respects of MLOG:

Declarative Query Language: It allows users to specify a range of machine learning models, including deep neural networks, very succinctly

Automated Query Optimization: Authors demonstrate how to automatically compile MLOG programs into native TensorFlow programs using textbook static analysis techniques.

Performance: The performance of automatically generated TensorFlow programs on a range of machine learning tasks.

##  THE MLOG LANGUAGE

- Algebra over Tensors.  The data model of MLOG is based on tensors–all data in MLOG are tensors and all operations are a subset of linear algebra over tensors. In MLOG, the tensors are closely related to the relational model; in fact, logically, a tensor is defined as a special type of relation. 
- TRules.  An MLOG program Π consists of a set of TRules(tensoral rules). 
- Semantics.   Similar to Datalog programs, we can define fixed point semantics for MLOG programs.
- Query.  There are two ways to query the system. The forward query and backward query.

## USER INTERACTION MODEL

Like most SQL databases, users interact with our system by executing a sequence of MLOG statements in a REPL or a script. Each MLOG statement can be either a standard SQL statement, a TView, an MLOG query, or an MLOG tensor construction statement. 

Query optimization is undertaken by first translating an MLOG program into a Datalog program, a process that we call “Datalogify.” Given the Datalog program, the optimizer uses a standard static analysis technique to reason about the property of the program and generate a TensorFlow program as the physical plan.

## RELATED WORK

Modern data systems often support libraries for analytics and machine learning. Examples include MADlib for Greenplum and PostgreSQL, SAP PAL for SAP HANA, ORE for Oracle databases. These libraries tightly integrate with the host data system and support traditional machine learning algorithms such as SVM or K-means. Authors advocates a more flexible higher-level language that supports more sophisticated machine learning models, such as deep neural networks, inside existing data systems. SciDB is a recent effort to extend relational database with data representations and operations for linear algebra. However, there is no machine learning library existing for SciDB and MLOG could fill that vacancy. There have been efforts to train linear models over joins. Compared with these efforts, MLOG advocates a more unified data model based on tensors instead of relations and also provides a more expressive way to encode correlations among tensors. As a result, MLOG is able to encode sophisticated machine learning models beyond linear models.

## CONCLUSION 

An MLOG program is very similar to a SQL program but extends relational algebra over relations to linear algebra over tensors. This extension allows MLOG to encode a range of machine learning models that are not supported in current data analytics systems. To optimize the performance of an MLOG program, MLOG contains a databasestyle query optimizer. In many cases, the resulting performance of automatically compiled MLOG programs is comparable with handtuned TensorFlow programs
