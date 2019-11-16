---
layout: page
subheadline: 任务调度-Quartz
title:  "Quartz学习笔记1-什么是Quartz"
teaser: "什么是Quartz？Quartz有什么优点？为什么要用Quartz"
meta_teaser: "This is an example of a beautiful aligned post in the middle. There is no sidebar to distract the reader. The difference to the Page-Template is, that you find meta-information at the bottom of the post."
breadcrumb: true
categories:
    - design
tags:
    - blog
    - content
    - post
    - post format
image:
    title: gallery-example-1.jpg
    # caption: Unsplash.com
    # caption_url: <http://unsplash.com>
author: mo
---
## 什么是Quartz？

 Quartz是功能强大的开源作业调度库，几乎可以集成到任何Java应用程序中-从最小的独立应用程序到最大的电子商务系统。Quartz可用于创建简单或复杂的计划，以执行数以万计，数以万计的工作。任务定义为标准Java组件的作业，它们实际上可以执行您可以对其执行的任何编程操作。Quartz Scheduler包含许多企业级功能，例如对JTA事务和集群的支持。 

## Quartz能做什么 ？

 作业调度对于一部分人员来说可能比较陌生，但是定时任务对于大部分开发人员来说，应该就都比较熟悉了。Quartz狭义上来说就是一个定时任务框架。当您的应用程序需要在某个给定的时间点执行程序，或者按照一定的规则，周期性的维护工作，Quartz可能是您理想的解决方案。

## 为什么要用Quartz

- 一款经过了时间检验的优秀开源框架，免费，真香！
- 可以**动态**的调度任务，功能完备的API，可以满足多种业务场景。
- 支持多线程、支持分布式和集群能力
- 支持多种持久化方式。 可以所有作业和触发器都存储在RAM中，也可以通过JDBC将 “非易失性”的作业和触发器都存储在关系数据库中。
- 可以参与、管理JTA事务
- 可以通过监听接口和插件机制来灵活的扩展
- 支持故障转移、错误补偿、负载均衡

## Spring定时任务是否可以替代Quartz

Spring3之后自带[Spring Task](https://docs.spring.io/spring/docs/5.2.1.RELEASE/spring-framework-reference/integration.html#scheduling)，因为其足够简单且除了Spring相关的包外，不需要引用额外的包，使用的人数也是越来越多。Spring Task可以将它看成是一个轻量级的Quartz，如果只是为了按照一定的规则定时的让系统去跑一些任务，且应用无需考虑分布式等情景，完全可以使用Spring自带的定时任务。

如果想动态的（增加、删除、暂停、恢复）调度任务、抑或想将任务调度的表达式存贮到数据库、系统是分布式系统等，Spring定时任务可能就不能完全的满足。虽然通过一些接口，或者自己去实现也可以实现上述需求，但是实现比较复杂，还是建议使用Quartz框架。

## 如何学习Quartz

接下来，我将连续发布几篇个人学习Quartz的笔记，来简单的介绍这个优秀的框架。同时我将会用SpringBoot来集成Quartz，希望能够在大家学习Quartz的过程中，可以提供一点帮忙。

此外，可以通过以下几个网站，来继续了解Quartz

- [Quartz官网](http://www.quartz-scheduler.org/)
- [GitHub]( https://github.com/quartz-scheduler/quartz )
- [W3Cschool](https://www.w3cschool.cn/quartz_doc/)