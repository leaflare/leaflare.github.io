+++
title = "Reading Notes on MLearn"
date = 2022-02-03 20:17:19
slug = "202202032017"

[taxonomies]
tags = ["DB4ML" ]
categories =  ["Reading Notes", "Eng"]

+++

Reading notes on https://doi.org/10.1145/3329486.3329494

MLearn: A Declarative Machine Learning Language for Database Systems

<!-- more -->

## ABSTRACT

The language was designed to cover an end-to-end machine learning process, including initial data curation, with the focus on
moving computations inside the core of database systems. In this paper, the authors explained the architecture of a compiler that translates into target specific user-defined-functions for the PostgreSQL and HyPer database systems. They gave an example on an accompanying example of linear regression. 

## INTRODUCTION

From a systems developer's point of view, database systems form the native way of efficiently storing data in index structures. Inside database systems, SQL, as the declarative language, simplifies data curation because it allows feature extraction as projections and selections of the only relevant tuples by design. Many studies have presented architectures for building end-to-end machine learning systems. Therefore, different systems tackle the challenges of representing arrays natively in database systems. 

The paper’s main contributions are the description of the architecture behind MLearn with an accompanying example, an extension of PostgreSQL by linear algebra and gradient descent on array datatypes, as well as a look on integrating array query languages.

##  THE MLEARN LANGUAGE

![](.\img\1.PNG)

Compiling the MLearn language with the ML2SQL compiler (dark blue): it first preprocesses import and include statements, then it compiles to SQL or Python code.

## TECHNICAL BACKGROUND

In order to allow machine-learning-related computations within database systems, they have to provide tensors and functionalities for training a model. HyPer has already extended its array datatype to serve as tensors by allowing algebra on those types. To reach a broader audience for our declarative machine learning language, they also provide some matrix algebra functionalities for PostgreSQL online. In addition to matrix operations, a gradient descent optimiser is essential for training models inside database systems.

## CONCLUSION AND ONGOING WORK

This paper has shown how the ML2SQL compiler treats preprocessor statements to allow the inclusion of code snippets and libraries.

They have discovered out that array processing represents the major building block for tasks related to machine learning. These tasks would strongly benefit from SQL especially for data preprocessing. In addition, when integrating the advantages of array database into hybrid OLTP and OLAP database systems, no domain specific systems would be required. We shall therefore work on applying matrix algebra to tables using stored procedures that are written in ArrayQL.
