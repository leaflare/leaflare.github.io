+++
title = "JDBC for MySQL"
date = 2022-01-14 20:17:19
slug = "202201142017"

[taxonomies]
tags = ["MySQL", "Spring Boot" ]
categories = ["Spring"]

+++

<!-- more -->

## 配置流程

### 在pom.xml加入JDBC driver mysql-connector-java 

```
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.7.29</version>
</dependency>
```

附MySQL版本查询命令：

```
mysql -V
```



## application.yml文件配置

### MySQL JDBC driver URL

```
protocol//[hosts][/database][?properties]
```

简单示例：

```
protocol – jdbc:mysql:
host – mysql.db.server:3306
database – my_database
properties – useSSL=false&serverTimezone=UTC
```

### 扩展

#### Protocol

除了"jdbc:mysql:"协议还支持：

```
Load-balancing JDBC connections – jdbc:mysql:loadbalance:
JDBC replication connections – jdbc:mysql:replication: 
```

#### Hosts

如果需要处理多个主机，可以在逗号分隔的列表中列出主机：host1，host2,..., hostN。

还可以用方括号将逗号分隔的主机列表括起来：[host1， host2,...,hostN]。

例如：

```
jdbc:mysql://myhost1:3306,myhost2:3307/db_name
jdbc:mysql://[myhost1:3306,myhost2:3307]/db_name
jdbc:mysql:loadbalance://myhost1:3306,myhost2:3307/db_name?user=dbUser&password=1234567&loadBalanceConnectionGroup=group_name&ha.enableJMX=true
```

#### Properties and User Credentials

有效的全局属性将应用于所有主机。

格式：

属性前面有一个问号"？"，并写成key=value键值对，由"&"符号分隔：

```
jdbc:mysql://myhost1:3306/db_name?prop1=value1&prop2=value2
```

可以将User Credentials放在属性列表中：

```
jdbc:mysql://myhost1:3306/db_name?user=root&password=mypass
```

可以在每个主机前面加上"user：password@host"格式：

```
jdbc:mysql://root:mypass@myhost1:3306/db_name
```

如果 JDBC URL 包含主机列表，并且所有主机都使用相同的User Credentials，则可以在主机列表前面加上前缀：

```
jdbc:mysql://root:mypass[myhost1:3306,myhost2:3307]/db_name
```

详细文档：

[MySQL :: MySQL Connector/J 8.0 Developer Guide :: 6.2 Connection URL Syntax](https://dev.mysql.com/doc/connector-j/8.0/en/connector-j-reference-jdbc-url-format.html)

[MySQL :: MySQL Connector/J 8.0 Developer Guide :: 6.3 Configuration Properties](https://dev.mysql.com/doc/connector-j/8.0/en/connector-j-reference-configuration-properties.html)



### MySQL连接示例

MySQL database： “*my_database*” 

host： “*mysql.db.server*“

```
@Test
public void givenMysqlDb_thenCreateConnectionObject() {
    String jdbcUrl = "jdbc:mysql://mysql.db.server:3306/my_database?useSSL=false&serverTimezone=UTC";    
    String username = "User";
    String password = "1234567";
    try (Connection conn = DriverManager.getConnection(jdbcUrl, username, password)) {
        assertNotNull(conn);
    } catch (SQLException e) {
        System.err.format("SQL State: %s\n%s", e.getSQLState(), e.getMessage());
    }
}
```



