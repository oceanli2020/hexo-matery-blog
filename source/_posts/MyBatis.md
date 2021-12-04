---
title: MyBatis
top: false
cover: false
toc: true
mathjax: true
date: 2021-03-14 16:57:42
password:
summary:
tags:
  - Java
  - MyBatis
categories:
  - Java
---

## MyBatis

JDBC 提供了 DataSource 接口、负责与数据库连接

实现了 DataSource 接口的类称为数据源，C3P0

**#{ }和${ }**
#{ }代表占位符，采用预编译方式传参，可以防止 SQL 注入攻击，对于字符串类型，自动添加引号
${ }直接拼接字符串，不能防止 SQL 注入攻击，对于字符串类型，不会自动添加引号，**所以**常用于模糊查询和排序

```sql
'%${name}%'
order by ${name}
```
