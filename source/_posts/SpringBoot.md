---
title: SpringBoot
top: false
cover: false
toc: true
mathjax: true
date: 2021-03-15 07:03:50
password:
summary:
tags:
  - Java
  - SpringBoot
categories:
  - Java
---

## SpringBoot

继承了 Spring 框架原有的优秀特性，同时通过简化配置进一步简化了 Spring 应用的搭建和开发过程。

可以基于 Maven 的创建独立的 Spring 应用程序，可以通过在 Maven 的 pom.xml 文件中添加相关依赖，来配置和管理相关依赖

SpringBoot 还内嵌了 Tomcat 容器，进一步减少了需要安装和配置 Tomcat 的过程

SpringBoot 在项目启动时，根据约定信息对组件进行加载、初始化，实现了开箱即用，提现了 SpringBoot 的设计理念，即约定大于配置

在实际开发中，SpingBoot 使用了经典的三层结构的软件架构，Cotroller 控制层，Service 业务层，Dao 数据访问层。Cotroller 层在接受前端发送的 URL 后，处理参数并返回结果，Service 层编写业务逻辑，处理数据，Dao 层访问数据库，进行增删改查

常用注解

```java
@RestController // 等价于 @ResponseBody ＋ @Controller 写在Controller类前替代@Controller
// 处理各自的传入请求方法类型
@GetMapping
@PostMapping
@PutMapping
@DeleteMapping
```
