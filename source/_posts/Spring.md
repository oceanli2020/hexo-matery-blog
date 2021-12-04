---
title: Spring
top: false
cover: false
toc: true
mathjax: true
date: 2021-03-14 17:31:13
password:
summary:
tags:
  - Java
  - Spring
categories:
  - Java
---

## Spring

SpringCore：核心容器（工厂模式）

特性：
`DI(依赖注入)`：给属性赋值
`IOC(控制反转)`：对象会自动注入，不用自己创建对象

常用注解：

在需要创建的对象前加上

```java
@Autowired  // Spring的注解
@Resource  // J2EE中的注解
```

然后再对应的类前加上

```java
@Service    // 业务层
@Component  // 泛指
@Repository // 持久层
@Controller // Web处理层
```

## AOP

AOP：面向切面变成的思想 - - - - - 为分散的对象引入公共行为，分离应用的业务逻辑和系统级服务，常用于日志和事务管理

需求：多个业务需要同一份代码，AOP 维护同一份代码，动态加入需要的内容

实现方式：（代理模式）

- JDK 动态代理：实现被代理类实现的接口，并重写接口中声明的方法

- 字节码增强：若被代理类没有实现接口，则继承被代理的类，并重写方法

通知：在切面的某个特定的连接点上执行的操作，即切入的内容

- 前置通知 before 在目标方法执行之前执行执行的通知

- 后置通知 after-renturn 在目标方法执行之后执行的通知

- 环绕通知 around 在目标方法执行之前和之后都可以执行额外代码的通知

- 抛出通知 after-throwing 在目标方法抛出异常时执行的通知

- 最终通知 after 是在目标方法执行之后执行的通知

常用注解：

```java
@Aspect // 切面类
@Pointcut // 配置切入点
@Before // 前置通知
@AfterReturning // 后置通知
@Around // 环绕通知
@AfterThrowing // 抛出通知
@After // 最终通知
```
