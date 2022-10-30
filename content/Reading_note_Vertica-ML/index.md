+++
title = "Reading Notes on Vertica-ML"
date = 2022-02-17 20:17:19
slug = "202202172017"

[taxonomies]
tags = ["DB4ML" ]
categories =  ["Reading Notes", "Eng"]

+++

Reading notes on  https://doi.org/10.1145/3318464.3386137

Vertica-ML: Distributed Machine Learning in Vertica Database

<!-- more -->

## ABSTRACT

An in-database machine learning system can provide many advantages in this scenario, e.g., eliminating the overhead of data transfer, avoiding the maintenance costs of a separate analytical system, and addressing data security and provenance concerns. This subsystem, Vertica-ML, includes machine learning functionalities with SQL API which cover a complete data science workflow as well as model management.  Machine learning models in Vertica are treated as first-class database objects like tables and views; therefore, they enjoy a similar mechanism for archiving and managing.

## INTRODUCTION

- An intuitive SQL interface for machine learning It treats models as first-class database objects, and allows business analysts and other SQL users to do more advanced data analysis using the same language they are comfortable with. 
- A variety of ML algorithms and tools In addition to distributed algorithms for training predictive models, the set of SQL functions introduced by Vertica-ML covers different stages of a data scientist’s typical workflow. 
- A special model object and model management In contrast to previous in-database ML systems which have been oblivious of model management, Vertica-ML provides capabilities to facilitate it. 
- Distributed in-memory storage It is integrated into the distributed architecture of Vertica DBMS with the ability to spill to disk when needed

## VERTICA ECOSYSTEM

![](.\img\1.PNG)

Firstly, the authors describe the main structure of Vertica. They emphasise that Vertica supports UDF (User Defined Function) and Metafunction, which is currently the more popular implementation in the industry and is the general idea in Presto ML.

Metafunction is a Vertica-specific function that differs from UDF in that UDF can be used as part of a SQL statement, whereas Metafunction can only be used after a SELECT keyword or even in a FROM clause. Metafunction can only be used after a SELECT keyword, not even in a FROM clause. In fact, the main purpose of Metafunction in Vertica-ML is to pass parameters to machine learning algorithms.

Vertica-ML also provides management functions for the trained models. The user can use get_model_summary and get_model_attribute to obtain the appropriate meta-information about the model. The model is stored as an object in a database table. This allows us to modify a model using the traditional ALTER or DROP statements.

Since the machine learning algorithms are implemented directly in the analytical database, parallel computing is a must. Because Vertica itself is a distributed multi-node database, each node computes a portion of the data and does aggregation on a single node.

