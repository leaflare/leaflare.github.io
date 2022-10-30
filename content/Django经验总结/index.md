+++
title = "Django经验总结"
date = 2021-11-25 12:55:12
slug = "202111251255"

[taxonomies]
tags = ["Django"]
categories = ["Python",  "Web"]

+++

<!-- more -->

## Django使用db.sqlite3

### 创建一个项目并启动

```
django-admin startproject xiangmuming
cd xiangmuming
python manage.py runserver
```

### 初始化admin账号和密码

- Django提供admin后台，便于统一管理用户、权限和权限组，超级用户初始化方法
- 创建管理员账户：

```
python manage.py createsuperuser
```

​		根据提示设置用户名、邮箱和密码，然后就可以用这个账号登录到后台

​		或者：

```
python manage.py createsuperuser --username=root --email=root@root.com
```

### 配置数据表

```
python manage.py migrate  #迁移django自带的表，有变更的表结构同步到数据库  后面不跟app默认更新所有的app变更
python manage.py makemigrations  #将有变更的表结构生成迁移
python  manage.py sqlmigrate+表名+序列号  #查询变更迁移的sql语句
```

### 
