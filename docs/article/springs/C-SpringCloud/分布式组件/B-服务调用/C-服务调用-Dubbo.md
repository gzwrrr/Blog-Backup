---
title: "服务调用-Dubbo"
shortTitle: "C-服务调用-Dubbo"
description: "服务调用-Dubbo"
icon: ""
author: 
  name: gzw
  url: 
  email: 1627121193@qq.com
isOriginal: false
date: 2022-06-01
category: 
- "分布式"
tag:
- "分布式"
sticky: 1
star: false
article: true
timeline: true,
dir:
  text: "服务调用-Dubbo"
  icon: ""
  collapsible: true
  index: true
  comment: true
headerDepth: 3
index: true
order: 2
copy:
  triggerWords: 100
  disableCopy: false
  disableSelection: false
feed:
  title: "服务调用-Dubbo"
  description: "服务调用-Dubbo"
  author:
    name: gzw
    email: 1627121193@qq.com
---





# 服务调用-Dubbo

## 架构设计

Dubbo 是一个高性能、轻量级的开源 RPC 框架，主要用于构建分布式服务应用。它支持多种协议、多种序列化方式和多种注册中心，提供了丰富的功能，包括负载均衡、容错、路由、服务降级等。Dubbo 的架构设计具有以下几个方面的特点：

1. 服务治理架构：Dubbo 的架构设计采用了服务治理的思想，将分布式服务治理问题抽象成了统一的架构模型。它提供了注册中心、配置中心、路由中心等多个核心组件，支持服务的注册与发现、配置管理、负载均衡等功能，大大简化了分布式服务治理的复杂度。
2. 高度可扩展性：Dubbo 的架构设计具有高度可扩展性，支持多种协议、多种序列化方式和多种注册中心，可以根据应用需求进行自由选择和配置。此外，Dubbo 还支持自定义协议、序列化方式和注册中心，可以快速扩展和定制化。
3. 面向接口设计：Dubbo 的架构设计采用了面向接口设计的思想，提供了统一的服务接口定义和服务提供方、消费方的契约协议，将服务接口、参数、返回值等信息进行了封装和统一管理。这种设计思想降低了服务间的耦合度，提高了服务的灵活性和可复用性。
4. 服务端异步处理：Dubbo 的架构设计采用了服务端异步处理的思想，支持异步调用和响应处理，提高了服务的并发性和吞吐量。此外，Dubbo 还支持多线程模型和线程池调度，可以快速响应大量并发请求。
5. 多种容错机制：Dubbo 的架构设计具有多种容错机制，包括服务降级、失败重试、熔断器等，可以有效提高服务的可用性和稳定性。此外，Dubbo 还支持多种负载均衡策略和路由策略，可以根据不同的应用场景进行灵活配置。

**不同层次的方案：**

1. Proxy 服务代理层：支持 JDK 动态代理、javassist 等代理机制
2. Registry 注册中心层：支持 Zookeeper 、Redis 等作为注册中心
3. Protocal 远程过程调用：支持 Dubbo、Http 等调用协议
4. Transport 网络传输层：支持 Netty、Mina 等网络传输框架
5. Serialize 数据序列化层：支持 JSON、Hessian 等序列化机制



## 负载均衡策略

1. Random Load Balance 随机负载均衡策略：随机选择一个可用的服务提供者。
2. Round Robin Load Balance 轮询负载均衡策略：按照顺序轮询选择可用的服务提供者。
3. Least Active Load Balance 最小活跃数负载均衡策略：选择当前活跃数最少的可用服务提供者，活跃数指当前处理请求的线程数。
4. Consistent Hash Load Balance 一致性哈希负载均衡策略：将每个服务提供者映射到一个哈希环上，根据请求的哈希值选择距离该哈希值最近的服务提供者。
5. Weighted Random Load Balance 加权随机负载均衡策略：在随机负载均衡策略的基础上，根据每个服务提供者的权重进行加权选择。
6. Weighted Round Robin Load Balance 加权轮询负载均衡策略：在轮询负载均衡策略的基础上，根据每个服务提供者的权重进行加权选择。
7. Sticky Load Balance 粘滞会话负载均衡策略：将同一个消费者的请求路由到同一个服务提供者，实现会话粘滞。





## 服务引入

Dubbo完成服务引入的过程如下：

1. 消费者引入依赖：在消费者端的项目中，需要引入 Dubbo 相关依赖，如 dubbo、dubbo-spring-boot-starter 等。
2. 配置消费者信息：在消费者端的项目中，需要配置 Dubbo 的消费者信息，包括注册中心地址、服务超时时间、服务调用重试次数等等。
3. 编写消费者代码：编写调用 Dubbo 服务的消费者代码，一般可以通过 @Reference 注解注入服务代理对象，然后像调用本地方法一样调用服务即可。
4. 启动消费者应用：启动消费者应用，Dubbo 将自动从注册中心订阅服务提供者的地址列表，并建立连接。
5. 远程调用：Dubbo 将消费者的请求通过网络协议发送给服务提供者，服务提供者接收请求并返回响应结果，Dubbo 再将响应结果返回给消费者端。

总的来说，Dubbo完成服务引入的过程就是将服务的代理对象注入到消费者的代码中，并通过网络协议实现远程调用。





## 服务导出

在Dubbo中，服务的导出是通过`Protocol`和`Exporter`两个关键组件来实现的。

首先，`Protocol`是Dubbo的核心组件之一，它定义了服务协议和网络传输方式，并通过`export`方法将服务导出为一个或多个`Exporter`对象，同时通过`refer`方法引入远程服务。Dubbo内置了多种协议，如Dubbo协议、RMI协议、Hessian协议、HTTP协议等，其中Dubbo协议是Dubbo的默认协议。

而`Exporter`则是一个代理对象，用于暴露服务接口，并负责接收和处理客户端请求。在Dubbo中，`Exporter`通常由`Protocol`的实现类来创建，它会将服务接口实现包装成一个`Invoker`对象，并将其绑定到一个网络端口上，以接收客户端请求。

具体的服务导出流程如下：

1. 首先，`Protocol`根据配置文件中指定的协议类型和端口号创建一个对应的实例，例如Dubbo协议的`DubboProtocol`实例；
2. 然后，`Protocol`通过`export`方法将服务接口和服务实现绑定到创建的实例上，并返回一个或多个`Exporter`对象；
3. `Exporter`负责将服务接口实现包装成一个`Invoker`对象，并将其绑定到指定的协议和端口上，等待客户端请求；
4. 客户端通过`ProxyFactory`创建一个远程服务代理对象，并发起远程调用请求；
5. `Invoker`接收到请求后，根据请求信息和服务接口实现执行相应的方法，并将结果返回给客户端。

需要注意的是，Dubbo支持多协议、多注册中心和多集群等场景，因此，在服务导出时，需要根据实际情况进行配置，以满足不同的业务需求。

