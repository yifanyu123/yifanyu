---
layout: post
title: '微服务:SpringCloudVsDubbo'
date: 2019-08-17
author: 小刺猬
color: '#83b582'
cover: '../assets/cover/5.JPG'
tags: 微服务 coding
---
# 微服务:SpringCloudVsDubbo
## 基本介绍
>  微服务架构是互联网很热门的话题，是互联网技术发展的必然结果。它提倡将单一应用程序划分成一组小的服务，服务之间互相协调、互相配合，为用户提供最终价值。虽然微服务架构没有公认的技术标准和规范或者草案，但业界已经有一些很有影响力的开源微服务架构框架提供了微服务的关键思路，例如Dubbo和Spring Cloud。各大互联网公司也有自研的微服务框架，但其模式都于这二者相差不大。

## 微服务主要的优势如下：
1. 降低复杂度
将原来偶合在一起的复杂业务拆分为单个服务，规避了原本复杂度无止境的积累。每一个微服务专注于单一功能，并通过定义良好的接口清晰表述服务边界。每个服务开发者只专注服务本身，通过使用缓存、DAL等各种技术手段来提升系统的性能，而对于消费方来说完全透明。
2. 可独立部署
由于微服务具备独立的运行进程，所以每个微服务可以独立部署。当业务迭代时只需要发布相关服务的迭代即可，降低了测试的工作量同时也降低了服务发布的风险。
3. 容错
在微服务架构下，当某一组件发生故障时，故障会被隔离在单个服务中。 通过限流、熔断等方式降低错误导致的危害，保障核心业务正常运行。
4. 扩展
单块架构应用也可以实现横向扩展，就是将整个应用完整的复制到不同的节点。当应用的不同组件在扩展需求上存在差异时，微服务架构便体现出其灵活性，因为每个服务可以根据实际需求独立进行扩展。

## 微服务思维导图
!["微服务思维导图"](https://yifanyu123.github.io/assets/微服务.png)
## 总体架构
- Dubbo
!["dubbo"](https://yifanyu123.github.io/assets/dubbo.png)
- Spring Cloud
!["SpringCloud"](https://yifanyu123.github.io/assets/eureka.png)
## 核心要素

|核心要素|Dubbo|	Spring Cloud|
|--|:--:|--:
服务注册中心	|Zookeeper Reddis	|Spring Cloud Netflix Eureka
服务调用方式|	RPC	|Rest API
服务网关	|无	|Spring Cloud Netflix Zuul
断路器	|/|Spring Cloud Netflix Hystrix
分布式配置	|/	|Spring Cloud Config
分布式追踪系统|	/	|Spring Cloud Sleuth
消息总线|	/	|Spring Cloud Bus
数据流|	/	|Spring Cloud Stream 基于Reddis,Rabbit,Kafka实现的消息微服务
批量任务|	/	|Spring Cloud Task

## 依赖协议

### Spring Cloud
- 使用HTTP协议的REST API

### Dubbo
- dubbo：Dubbo缺省协议采用单一长连接和NIO异步通讯，适合于小数据量大并发的服务调用，以及服务消费者机器数远大于服务提供者机器数的情况
- rmi：RMI协议采用JDK标准的java.rmi.实现，采用阻塞式短连接和JDK标准序列化方式
- Hessian:Hessian协议用于集成Hessian的服务，Hessian底层采用Http通讯，采用Servlet暴露服务，Dubbo缺省内嵌Jetty作为服务器实现
- http:采用Spring的HttpInvoker实现

## Spring Cloud 工作流程
!["springCLoud工作流程"](https://yifanyu123.github.io/assets/SpringCloud.png)



> created: 2019/08/17
> updated: 2019/08/17
> 未完待续
