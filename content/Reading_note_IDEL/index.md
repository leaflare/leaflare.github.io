+++
title = "Reading Notes on IDEL"
date = 2022-03-01 20:17:19
slug = "202203012017"

[taxonomies]
tags = ["DB4ML"]
categories =  ["Reading Notes", "Eng"]

+++

Reading notes on https://doi.org/10.48550/arXiv.1803.04884

IDEL: In-Database Entity Linking with Neural Embeddings

<!-- more -->

## ABSTRACT

 In-Database Entity Linking (IDEL), abstracts core tasks of most neural entity linking systems for MonetDB, leverages the ability of MonetDB to support in-database-analytics with user defined functions (UDFs) implemented in Python. These functions call machine learning libraries for neural text mining, such as TensorFlow. The system achieves zero cost for data shipping and transformation by utilizing Monet DB's ability to embed Python processes in the database kernel and exchange data in NumPy arrays. IDEL represents text and relational data in a joint vector space with neural embeddings and can compensate errors with ambiguous entity representations. For detecting matching entities, the authors propose a novel similarity function based on joint neural embeddings which are learned via minimizing pairwise contrastive ranking loss. This function utilizes a high dimensional index structures for fast retrieval of matching entities. 

## INTRODUCTION

Entity linking between text and tables

The need for Neural Entity Linking in RDBMSs

 the following characteristics are realized in the IDEL architecture:

**Best of two worlds** Users can seamlessly switch between SQL and Python, so that they can choose the best execution environment for each part of their data analytics. 

**Flexible and extensible** IDEL provides a set of pre-trained neural network models. In addition, it permits users to plugin their own models or third-party models for entity linking. 

**Simple SQL-based user interface** IDEL provides an SQL-based user interface to all parts of the system. The whole workflow of entity linking can be executed by several calls to the implemented SQL UDFs. All intermediate and final results can be stored in the underlying database for further analysis. 

**Robust to language errors** IDEL adopts state-of-the-art neural embeddings for entity linking, which can achieve much higher precision under the four typical error sources (i.e. homonyms, hyponyms, synonyms and misspellings). In addition, the system leverages extra information from the relational data, such as attribute values, and integrity constraints on data type and range. 

**No manual feature engineering** IDEL does not require manual feature engineering;instead the system observes data distributions in the text database to represent best entities in relational and text data.

## RELATED WORK

Text Databases 

Embeddings in Databases

Entity Linking and knowledge base completion

