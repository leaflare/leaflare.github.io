+++
title = "MySQL使用经验总结"
date = 2022-01-04 20:37:19
slug = "202201042037"

[taxonomies]
tags = ["MySQL" ]
categories = ["MySQL"]

+++

<!-- more -->

## 语句相关

### MySQL防止重复插入相同记录

#### 使用MySQL 索引

创建索引

```sql
CREATE UNIQUE INDEX index_name ON tablename( index_column_1, index_column_2 );
```

在一个已存在表中添加一个唯一索引

```sql
ALTER TABLE table_name ADD INDEX index_name( index_column_1, index_column_2 ) ;
```

此时插入重复插入已有索引的记录会出现报错`IntegrityError: (1062, "Duplicate entry 'xx' for key 'xxxxx'")`

解决方法：把`INSERT INTO` 改为 `INSERT IGNORE INTO

## 报错处理

### ERROR 1064 (42000): You have an error in your SQL syntax

在MySQL中，为了区分MySQL的关键字与普通字符，MySQL引入了一个反引号。

（就是键盘左上角数字1左边的那个

使用了MySQL保留字或关键字却没有用反引号就会报这个错误。

如果列名使用了MySQL保留字或关键字就要用反引号代替引号，注释不能用反引号，因为注释只是普通字符串，不是MySQL的关键字，只有关键字或保留字才能使用反引号。



## PyMySQL

### 报错处理

#### 接口形式报错

事故现场：

![](.\img\pymysql_error.png)

解决：

![](.\img\pymsql_solve.png)

## ubuntu环境的MySQL

### 修改密码

查看mysql数据库自动设置的随机账户与密码

```
sudo cat /etc/mysql/debian.cnf
```

使用这组账号密码登录

```
mysql -u debian-sys-maint -p
```

切换到mysql数据库

```
use mysql;
```

这里有个巨坑

mysql 5.7.9以后废弃了password字段和password()函数；authentication_string:字段表示用户密码，而authentication_string字段下只能是mysql加密后的41位字符串密码。

**正确的解决方法：**

将字段置为空

```
update user set authentication_string='' where user='root';      
```

```
alter user 'root'@'localhost' identified with mysql_native_password by 'root';
```

最后的'root'是新密码

修改后`quit`

重新登录

```
service mysql restart
```

```
mysql -u root -p
```

## 概念和原理

### MySQL索引

[索引](https://www.cnblogs.com/yrxing/p/14557150.html)
