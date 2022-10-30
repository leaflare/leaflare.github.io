+++
title = "Reading Notes on XuanYuan"
date = 2022-02-21 20:17:19
slug = "202202212017"

[taxonomies]
tags = ["DB4ML" , "ML4DB"]
categories =  ["Reading Notes", "Eng"]

+++

Reading notes on Li G, Zhou X, Li S. XuanYuan: An AI-Native Database[J]. IEEE Data Eng. Bull., 2019, 42(2): 70-81.

<!-- more -->

## ABSTRACT

On one hand they integrate AI techniques into databases to provide self-configuring, self-optimizing, self-monitoring, self-diagnosis, self-healing, self-assembling, and self-security capabilities. On the other hand, they enable databases to provide AI capabilities using declarative languages in order to lower the barrier of using AI.

In this paper, they also introduce five levels of AI-native databases and provide several open challenges of designing an AI-native database. 

## INTRODUCTION

The five layers of AI native databases.

Databases play a very important role in many applications and are widely deployed in many domains. Over the last fifty years, databases have undergone three major revolutions.

The first generation was the standalone database, which solved the problems of data storage, data management and query processing. Representative systems include PostgreSQL and MySQL.

The second generation is the clustered database, which aims to provide high availability and reliability for business-critical applications. Representative systems include Oracle RAC, DB2 and SQL server.

The third generation is distributed databases (and cloud-native databases), which aim to solve the problems of elastic computing and dynamic data migration in the era of big data. Representative systems include Aurora and GaussDB.

However, in the era of Big Data, traditional databases still have some limitations due to the large scale of data, the variety of applications/users and the diverse computing power.

Traditional database design is still based on empirical methods and specifications, and requires significant human involvement (e.g. DBAs) to tune and maintain the database.

Traditional databases focus on the relational model and provide relational data management and analysis capabilities.

Transitional databases only consider generic hardware such as CPU, RAM and disk, but do not take full advantage of new hardware such as ARM, AI chips, GPUs, FPGAs, NVM, RDMA.

Regulus integrates AI technology into the database to make it more intelligent and also provides AI capabilities within the database. On the one hand, Regulus integrates AI technology into the database to provide self-configuration, self-optimisation, self-monitoring, self-diagnosis, self-healing, self-security and self-assembly capabilities, which can improve the availability, performance and stability of the database and reduce the burden of intensive human involvement. On the other hand, "Regulus" enables databases to provide AI capabilities using declarative languages to lower the threshold for using AI. In addition, "Regulus" leverages diverse computing power to support data analysis and machine learning.



![](.\img\1.PNG)

## AI4DB

There are several challenges to embedding AI capabilities in a database.
Training samples. Most AI models require large-scale, high-quality, diverse training data to achieve good performance. However, it is quite difficult to obtain training data in databases because they are either safety-critical or dependent on DBAs. for example, in database button tuning, the training samples should be based on the experience of DBAs. Therefore, it is difficult to obtain a large number of training samples. In addition, the training data should cover different scenarios, different hardware environments, and different workloads.
Model selection. There are many machine learning algorithms and it is difficult to automatically select one for different scenarios. In addition, model selection is influenced by many factors, such as
quality, training time, adaptability, and generalisation. For example, deep learning may be a better choice for cost estimation, while reinforcement learning may be a better choice for estimation, while reinforcement learning may be a better choice for connection order selection. Training time may also be important, as some applications have high performance requirements and cannot tolerate long training periods.
Model convergence. It is very important that the model converges. If the model does not converge, we need to provide alternative methods to avoid making the wrong decision. For example, in knob tuning, if the model does not converge, we cannot use the model to make knob suggestions.
Adaptability. The model should be adaptable to different scenarios. For example, if the hardware environment changes, the model can be adapted to the new hardware.
Generalisability. The model should be adaptable to different database settings. For example, if the workload is changed, the model should support the new workload. If data is updated, the model should be generalised to support the new data.



## DB4AI

**Accelerate AI algorithms using indexing techniques.** Most of studies focus on the effectiveness of AI algorithms but do not pay much attention to the efficiency, which is also very important. It calls for utilizing database
techniques to improve the performance of AI algorithms. For example, self-driving vehicles require a large number of examples for training, which is rather time consuming. Actually, it only requires some important
examples, e.g., the training cases in the night or rainy day, but not many redundant examples. Thus we can index the samples and features for effective training.
**Discover AI Models.** Ordinary users may only know their requirements, e.g., using a classification algorithm to address a problem, but do not know which AI algorithms should be used. Thus it is important to automatically discover AI algorithms. Moreover, it is also challenging to reuse the well-trained AI models by different users.

## Edge Computing Database

Most databases are designed to be deployed on servers. With the development of 5G and IOT devices, it calls for a tiny database embedded in small devices. There are several challenges in designing such a tiny database. The first is database security to protect the data. The second is real-time data processing. The small device has low computing power, and it is rather challenging to provide high performance on such small devices. The third is data migration among different devices. Some devices have small storage and it is challenging to migrate the data across different devices.
