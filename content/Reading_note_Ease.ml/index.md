+++
title = "Reading Notes on Ease.ml"
date = 2022-03-05 20:17:19
slug = "202203052017"

[taxonomies]
tags = ["DB4ML"]
categories =  ["Reading Notes", "Eng"]

+++

Reading notes on 

Ease.ml: towards multi-tenant resource sharing for machine learning workloads

https://doi.org/10.1145/3177732.3177737

Ease.ml/ci and Ease.ml/meter in action: towards data management for statistical generalization

https://doi.org/10.14778/3352063.3352110

<!-- more -->

## Question

As a "service provider" that manages a shared cluster of machines running machine learning workloads, what is the resource sharing strategy that maximizes the global satisfaction of all users? Resource sharing is a critical yet subtle issue in this multi-tenant scenario, as we have to balance between efficiency and fairness. Also, as of today, the counterpart of "software engineering for ML" is largely missing --- developers of ML applications are left with powerful tools (e.g., TensorFlow and PyTorch) but little guidance regarding the development lifecycle itself.

## Ease.ml

Ease.ml, a declarative machine learning service platform. With ease.ml, a user defines the high-level schema of an ML application and submits the task via a Web interface. The system then deals with the rest, such as model selection and data movement. They first formalize the problem that we call multi-tenant model selection, aiming for minimizing the total regret of all users running automatic model selection tasks. Then  they develop a novel algorithm that combines multi-armed bandits with Bayesian optimization and prove a regret bound under the multi-tenant setting. Finally, they report the evaluation of ease.ml on synthetic data and on two services we are providing to our users, namely, image classification with deep neural networks and binary classification with Azure ML Studio.

## Ease.ml/ci and Ease.ml/meter

Developing machine learning (ML) applications is similar to developing traditional software --- it is often an iterative process in which developers navigate within a rich space of requirements, design decisions, implementations, empirical quality, and performance. In traditional software development, software engineering is the field of study which provides principled guidelines for this iterative process.

The authors look at the management of the ML development lifecycle from a data management perspective. They show two closely related systems, easy.ml/ci and easy.ml/meter, which provide some 'principled guidance' for ML application development: ci is a continuous integration engine for ML models, and meter is a 'profiler' for controlling overfitting of ML models. Both systems focus on managing the 'statistical generalisation capabilities' of the datasets used to assess the quality of ML applications, i.e. the validation set and the test set.
