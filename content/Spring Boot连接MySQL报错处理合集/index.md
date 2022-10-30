+++
title = "Spring Boot连接MySQL报错处理合集"
date = 2022-01-12 19:33:41
slug = "202201121933"

[taxonomies]
tags = ["MySQL", "Spring Boot" ]
categories = ["Spring"]

+++

<!-- more -->

## 数据库配置报错

### HikariPool-1 - Exception during pool initialization.

HikariPool是SpringBoot默认数据库连接池，该报错说明数据库配置有问题

在application.yml配置文件中逐项检查username、password、url中IP地址、端口号等是否正确



## jdbc url踩坑

### com.mysql.cj.jdbc.exceptions.CommunicationsException: Communications link failure

重新在本地跑以前部署过的项目，遇到这个问题

解决：把jdbc url的ip地址改成localhost:3306

3306 ：SQL数据库的端口

### javax.net.ssl.SSLException MESSAGE: closing inbound before receiving peer's close_notify

使用mysql8.0以上版本数据库，要设置useSSL=false否则会出现上述报错

示例：

```
url：jdbc:mysql://localhost:3306/database_name?useSSL=false
```

反之，mysql8.0以下版本例如mysql5.7如果url包含useSSL，也可能会报错，删除该项即可解决

### The server time zone value ‘????’ is unrecognized

时区设置serverTimezone=GMT%2B8

示例：

```
url：jdbc:mysql://localhost:3306/database_name?serverTimezone=GMT%2B8
```

也可以在application.yml “spring:”下添加：

```
jackson:
    time-zone: GMT+8
```

