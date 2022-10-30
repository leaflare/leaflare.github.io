+++
title = "Reading Notes on SQML"
date = 2022-02-15 20:17:19
slug = "202202152017"

[taxonomies]
tags = ["DB4ML" ]
categories =  ["Reading Notes", "Eng"]

+++

Reading notes on  https://doi.org/10.1145/3127479.3132746

SQML: large-scale in-database machine learning with pure SQL

<!-- more -->

## ABSTRACT

Many enterprises have migrated their data from an on-site database to a cloud-based database-as-a-service that handles all database-related administrative tasks while providing a simple SQL interface to the end user. Given these converging trends, there is a pressing need for database-as-a-service providers to add support for sophisticated machine learning algorithms to the core functionality of their products.

## INTRODUCTION

SQML has several advantages over existing in-database machine learning methods, especially for a database-as-a-service:

- SQML does not require user-dened aggregates (UDAs). 
- Even when UDAs are available, UDA-based approaches assume that the learned model can ?t in memory on a single machine. SQML represents models as disk-backed tables that are partitioned across many machines, so it can scale to arbitrary model sizes.
- SQML estimates generalized linear models, a large class of models for supervised machine learning that includes linear regression, logistic regression, and support vector machines as special cases. 

## CONCLUSION 

The SQML learning algorithm is based on preconditioned proximal gradient descent, a state-of-the-art method for convex optimization.

The results of the experiment are shown SQML outperformed the UDA-based algorithm.

First, Dremel imposes relatively tight memory limits on each user, and since UDAs must hold the entire model in memory, they had to use model compression techniques that invariably degrade model quality.  SQML does not require model compression, since the models are stored in disk-backed tables. 

Second, on large training sets UDA-based algorithms learn several independent models on disjoint subsets of the training data and then average them together, an approach that slows convergence. SQML always learns a single model on the entire dataset
