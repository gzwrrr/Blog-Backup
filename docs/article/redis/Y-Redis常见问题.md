---
title: "Redis 常见问题"
shortTitle: "Y-Redis 常见问题"
description: "Redis 常见问题"
icon: ""
author: 
  name: gzw
  url: 
  email: 1627121193@qq.com
isOriginal: false
date: 2022-06-19
category: 
- "redis"
- "数据库"
- "缓存"
tag:
- "redis"
- "数据库"
- "缓存"
sticky: 1
star: false
article: true
timeline: true,
dir:
  text: "Redis 常见问题"
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
  title: "Redis 常见问题"
  description: "Redis 常见问题"
  author:
    name: gzw
    email: 1627121193@qq.com
---





# Redis 常见问题


[[toc]]

## 为什么单线程的 Redis 能那么快？

Redis的瓶颈主要在IO而不是CPU，所以为了省开发量，在6.0版本前是单线程模型；其次，Redis 是单线程主要是指 **Redis 的网络 IO 和键值对读写是由一个线程来完成的**，这也是 Redis 对外提供键值存储服务的主要流程。（但 Redis 的其他功能，比如持久化、异步删除、集群数据同步等，其实是由额外的线程执行的）。

Redis 采用了多路复用机制使其在网络 IO 操作中能并发处理大量的客户端请求，实现高吞吐率。



## 如何保持 Redis 和 MySQL 数据一致？

1. 先更新 MySQL 再更新 Redis，但是如果更新 Redis 失败也还是可能不一致
2. 先删除 Redis 中的数据，再更新 MySQL，再次查询时再将数据添加到缓存中。这种方案再高并发下性能比较低，而且仍然有数据不一致的问题
3. 延时双删：先删除 Redis 缓存数据，再更新 MySQL，延迟几百毫秒再删除 Redis 的缓存数据，这样就算在更新 MySQL 时还有其他线程将老数据写入 Redis 中，那么也会被删除，从而保持数据一致性





## 什么是红锁？

红锁（Redlock）是一个由Redis的作者提出的分布式锁算法，旨在解决在分布式系统中锁的可用性问题。

红锁算法的基本思路是，在一个分布式系统中，利用多个Redis节点来共同协作实现分布式锁。当一个客户端需要获取锁时，首先需要尝试在一定数量的Redis节点上获取锁，并且在获取锁时需要指定一个过期时间。当这些Redis节点成功获取锁并且过期时间没有到达时，客户端才算成功获取锁。在释放锁时，客户端需要向所有Redis节点发送解锁请求，并且只有当大多数的节点都解锁成功时，才算真正释放锁。

红锁算法的使用场景是在分布式系统中需要高可用性的锁服务，可以避免单点故障或者单一的锁服务节点瓶颈问题。但是需要注意的是，红锁算法并不能完全避免竞争条件，因此需要在具体使用时仔细考虑。





## 有什么编程模型？

| 编程模型         | 解释                                                         | 适用场景                                             |
| ---------------- | ------------------------------------------------------------ | ---------------------------------------------------- |
| 面向过程编程模型 | 以过程或函数为中心，按照自上而下的方式逐步执行指令。         | 适合简单的应用场景                                   |
| 面向对象编程模型 | 将程序设计看作一组对象的集合，每个对象都有自己的状态和行为，通过相互协作实现功能。 | 适合复杂的应用场景                                   |
| 函数式编程模型   | 函数是编程的基本单位，程序由一系列函数组成。强调无副作用、不可变性等特性。 | 适合处理复杂的逻辑和数据                             |
| 响应式编程模型   | 通过将事件流和数据流视为基本组件，实现高度并发和响应性能力。 | 适合处理高并发、低延迟的应用场景                     |
| 并发编程模型     | 使用多线程、协程等技术，实现并发执行程序的能力。             | 适合处理高并发、高吞吐量的应用场景                   |
| 事件驱动模型     | 通过将事件作为驱动程序的中心，异步处理事件并根据事件进行响应。 | 适合处理异步IO、高并发、低延迟的应用场景，如网络编程 |





## 网络编程模型有哪几种？

| 模型名称                                | 描述                                                         |
| --------------------------------------- | ------------------------------------------------------------ |
| 阻塞式 I/O（（Blocking I/O Model））    | 程序在读取或写入数据时会一直等待，直到数据传输完成才会返回结果。 |
| 非阻塞式 I/O（Non-blocking I/O Model）  | 程序会立即返回一个结果，不会等待数据传输完成。但程序需要轮询操作的状态，判断操作是否完成。 |
| I/O 复用模型（I/O Multiplexing Model）  | 通过一个线程来监听多个 I/O 操作的状态，当某个操作就绪时再进行处理。常见的实现方式是使用 select、poll、epoll 等机制。 |
| 信号驱动模型（Signal-driven I/O Model） | 程序使用信号来通知事件状态的改变。                           |
| 异步 I/O（Asynchronous I/O Model）      | 程序发起一个 I/O 操作后，继续执行后续操作，当 I/O 操作完成后再通知程序。通知方式一般是回调函数或者类似协程的方式。 |





## 什么是事件驱动？

事件驱动（Event-Driven）是一种编程模型，它是指系统或程序在执行过程中，以事件触发为基础来完成任务和执行操作。它的核心思想是将任务和操作看作是事件，通过在程序中定义事件和事件处理器，当某个事件发生时，调用相应的事件处理器来处理事件。

在事件驱动模型中，通常有一个事件循环（Event Loop）在运行，它会不断地从一个事件队列中取出事件并调用相应的处理器来处理事件。这种模型相比传统的同步模型更加高效，因为它不需要等待一个操作完成才能执行下一个操作，而是可以并发处理多个事件。

事件驱动模型常见于各种异步编程中，例如 GUI 编程、网络编程等。它也是许多开源项目和框架的基础，例如 Node.js、Netty 等。

<br/>

事件驱动是一种编程模型，其中程序流程是由事件和事件处理器（也称为回调函数或观察者）来驱动的。当程序执行到等待某个事件的位置时，它不会一直阻塞等待该事件完成，而是注册一个回调函数并继续执行其他任务，等待事件就绪时会触发回调函数的执行。这种方式可以让程序并发处理多个事件，从而提高程序的性能和响应能力。

常见的事件驱动编程模型包括：

1. Reactor模型：使用一个线程循环监听多个输入源，当输入就绪时通知对应的处理器进行处理。
2. Proactor模型：使用多个线程来并发处理请求，每个请求处理器维护一个输入队列和一个输出队列，将输入的数据请求加入输入队列，处理器从输入队列中读取请求并进行处理，将输出结果放入输出队列中，最后由另一个线程来将输出结果返回给客户端。
3. Actor模型：通过消息传递的方式来实现并发，每个Actor是一个独立的处理单元，处理接收到的消息并可以向其他Actor发送消息。

这些模型都是基于事件驱动的方式来实现并发处理的，但它们的实现方式略有不同。





## 非阻塞式 I/O、I/O 复用模型、异步 I/O模型的区别

非阻塞式 I/O、I/O 复用模型、异步 I/O 模型都是解决I/O效率低下的问题的技术方案，它们的主要区别在于I/O的操作方式和应用程序与操作系统之间的交互方式。

1. 非阻塞式 I/O：
   - I/O操作为非阻塞式的，即I/O操作不会阻塞进程继续执行，但进程仍需要轮询检查I/O操作是否完成；
   - 进程需要不断轮询I/O操作是否完成，如果未完成则继续执行其他操作；
   - 适用于连接数较少、每个连接数据量小的场景。
2. I/O 复用模型：
   - 利用select、poll、epoll等机制，使一个进程可以同时检查多个socket的状态，从而在有数据可读写时才进行I/O操作；
   - 进程通过系统调用将自己的一组I/O操作集合注册到内核中，然后阻塞在 select/poll/epoll 等函数上，直到其中任何一个I/O操作完成；
   - 适用于连接数较多、每个连接数据量不大的场景。
3. 异步 I/O 模型：
   - I/O 操作的结果由内核通知进程，进程无需阻塞等待I/O操作完成；
   - 进程发起I/O操作之后，就可以继续执行其他操作，当I/O操作完成后，内核会通知进程；
   - 适用于连接数较多、每个连接数据量较大的场景。

下表列出了三种模型的特点和适用场景：

|              | 非阻塞式 I/O               | I/O 复用模型                 | 异步 I/O 模型                |
| ------------ | -------------------------- | ---------------------------- | ---------------------------- |
| I/O 操作方式 | 非阻塞式                   | 阻塞式                       | 异步                         |
| 进程操作方式 | 轮询                       | 阻塞等待                     | 发起后继续                   |
| 适用场景     | 连接数少、每个连接数据量小 | 连接数多、每个连接数据量不大 | 连接数多、每个连接数据量较大 |





## netty、libevent、redis的关系？

Netty、libevent和Redis都是事件驱动的网络编程框架/库，它们的关系如下：

1. Netty：是一个开源的、高性能的、异步的、事件驱动的网络应用框架，支持多种协议，比如HTTP、WebSocket、TCP等。Netty底层使用Java NIO，利用多路复用技术实现高并发网络通信，提供了很多高级的特性，比如TCP拆包粘包处理、流量控制、超时管理等。
2. libevent：是一个高性能的事件驱动库，由Facebook开发，底层使用C语言实现，支持多种操作系统，包括Linux、macOS、Windows等。libevent可以提供高并发的I/O、定时器和信号处理，被广泛应用于高性能的服务器程序，比如memcached、Redis等。
3. Redis：是一个开源的、高性能的、非关系型的key-value数据库，支持多种数据结构，比如字符串、哈希、列表、集合、有序集合等。Redis底层使用C语言实现，使用了多种技术来提高性能，比如多路复用、内存池等。Redis使用事件驱动的方式来实现网络通信，底层的事件驱动库是ae（An Event library，由其自己实现）

相对于其他成熟的事件驱动库，ae更加简洁、轻量，只包含了Redis需要用到的事件类型和相关的函数，能够满足Redis的需求，同时降低了代码的复杂度。

Redis的ae事件驱动库支持多种事件类型，包括文件事件（file event）、定时事件（time event）和延迟事件（delay event）。其中，文件事件是最基本的事件类型，用于监听文件描述符上的读写事件。定时事件则是指定一个时间，当时间到达时触发事件。延迟事件则是指定一个延迟时间，在延迟时间到达之后触发事件。

ae事件驱动库的核心是aeMain函数，它是事件驱动的入口，主要用于监听并处理所有的事件。当某个事件被触发时，aeMain会调用相应的回调函数来处理事件。此外，ae事件驱动库还提供了其他的一些函数，如aeCreateFileEvent、aeCreateTimeEvent等，用于注册、创建事件。

虽然Redis的ae事件驱动库相对简单，但它仍然能够满足Redis的高性能、高并发的需求。





## 为什么主从全量复制使用RDB而不使用AOF？

1、RDB文件内容是经过压缩的二进制数据（不同数据类型数据做了针对性优化），文件很小。而AOF文件记录的是每一次写操作的命令，写操作越多文件会变得很大，其中还包括很多对同一个key的多次冗余操作。在主从全量数据同步时，传输RDB文件可以尽量降低对主库机器网络带宽的消耗，从库在加载RDB文件时，一是文件小，读取整个文件的速度会很快，二是因为RDB文件存储的都是二进制数据，从库直接按照RDB协议解析还原数据即可，速度会非常快，而AOF需要依次重放每个写命令，这个过程会经历冗长的处理逻辑，恢复速度相比RDB会慢得多，所以使用RDB进行主从全量复制的成本最低。

2、假设要使用AOF做全量复制，意味着必须打开AOF功能，打开AOF就要选择文件刷盘的策略，选择不当会严重影响Redis性能。而RDB只有在需要定时备份和主从全量复制数据时才会触发生成一次快照。而在很多丢失数据不敏感的业务场景，其实是不需要开启AOF的。





## 为什么还有无磁盘复制模式？

Redis 默认是磁盘复制，但是**如果使用比较低速的磁盘，这种操作会给主服务器带来较大的压力**。Redis从2.8.18版本开始尝试支持无磁盘的复制。使用这种设置时，子进程直接将RDB通过网络发送给从服务器，不使用磁盘作为中间存储。

**无磁盘复制模式**：master创建一个新进程直接dump RDB到slave的socket，不经过主进程，不经过硬盘。适用于disk较慢，并且网络较快的时候。

使用`repl-diskless-sync`配置参数来启动无磁盘复制。

使用`repl-diskless-sync-delay` 参数来配置传输开始的延迟时间；master等待一个`repl-diskless-sync-delay`的秒数，如果没slave来的话，就直接传，后来的得排队等了; 否则就可以一起传。



## 为什么还会有从库的从库的设计？

以级联的方式减轻主库的传输压力





## Redis 读写分离需要注意的问题

> 先用集群或者其他方式优化，最后才考虑使用读写分离提高负载能力

1. 延迟与不一致问题，可以通过设置相关参数调整
2. 数据过期问题，有 **惰性删除** 和 **定期删除**，主节点删除，从节点一般都不会主动删除数据。Redis 3.2 之后从节点读取数据前会先检查数据是否过期，过期则不会返回给客户端
3. 故障切换问题，可以使用哨兵模式或者手动切换

在使用读写分离之前，可以考虑其他方法增加Redis的读负载能力：如尽量优化主节点（减少慢查询、减少持久化等其他情况带来的阻塞等）提高负载能力；使用Redis集群同时提高读负载能力和写负载能力等。如果使用读写分离，可以使用哨兵，使主从节点的故障切换尽可能自动化，并减少对应用程序的侵入





## 一致性哈希与哈希槽

一致性哈希（Consistent Hashing）是一种解决分布式系统中负载均衡的算法。在传统的哈希算法中，数据被分配到固定的节点上，而在分布式系统中，节点数量可能动态变化，当新增或删除节点时，传统的哈希算法需要重新计算哈希值，重新分配数据，这会导致大量数据迁移，对系统的性能和稳定性产生影响。而一致性哈希算法可以解决这个问题。

一致性哈希将哈希值映射到一个环形空间上，每个节点在环上有一个哈希值，数据被映射到环上的某个位置，然后顺时针寻找到第一个节点，将数据存储到这个节点上。当节点数量发生变化时，只有与新增或删除节点相邻的部分数据需要重新映射到新的节点上，而其他数据的映射不会受到影响，从而减少了数据迁移的次数和影响。

与哈希槽的区别在于，哈希槽是Redis Cluster用于分片的一个概念，将整个数据空间分为16384个哈希槽。每个节点可以负责多个哈希槽，一个哈希槽只会被一个节点负责。Redis Cluster使用哈希槽的方式进行数据的划分和复制，而一致性哈希算法只是一种算法，它可以被用于分布式系统中的负载均衡、缓存、消息队列等场景。





## Redis 为什么使用哈希槽？

Redis使用哈希槽的目的是为了实现数据的分片和负载均衡。

在Redis Cluster中，整个数据空间被分为16384个哈希槽，每个节点可以负责多个哈希槽。当客户端向Redis Cluster发送一个命令时，Redis Cluster首先根据命令所涉及的key计算出对应的哈希槽编号，然后确定负责这个哈希槽的节点，最后将命令转发给该节点进行处理。

使用哈希槽的好处是可以实现数据的自动分片和负载均衡，使得Redis Cluster可以方便地扩展到多个节点，处理更大规模的数据和请求。此外，使用哈希槽还可以降低数据迁移的代价，当节点发生故障或扩容缩容时，只有部分哈希槽需要迁移，而不是所有数据都需要迁移。

总之，使用哈希槽可以为Redis Cluster提供更好的扩展性、性能和可用性。

使用一致性哈希也可以实现分片和负载均衡，但是相比较而言，使用哈希槽可以提供更好的扩展性和可靠性。

使用一致性哈希时，每个节点负责的哈希范围是通过计算哈希值在一致性哈希环上的位置来确定的。这种方法虽然可以平均地分配数据，但是当节点的数量变化时，由于哈希环上的节点数量发生变化，每个节点负责的哈希范围也会发生变化，从而导致大量的数据需要重新分配和迁移。而使用哈希槽可以避免这种问题，因为每个节点负责的哈希槽的编号是固定的，即使节点数量变化，每个节点负责的哈希槽的编号也不会变化，只需要迁移一部分数据即可。

另外，使用哈希槽还可以实现更细粒度的负载均衡，因为哈希槽的数量可以根据实际情况进行调整，可以根据数据的分布情况进行合理分配，从而避免某些节点负载过重的情况。而使用一致性哈希时，负载均衡的粒度相对较大，无法进行精细的调整。

综上所述，使用哈希槽可以提供更好的扩展性和可靠性，因此Redis Cluster选择使用哈希槽来实现分片和负载均衡。







## Cluster 总线

Redis Cluster的总线是一种用于节点之间通信的内部协议。Redis Cluster使用总线来实现节点之间的消息传递和状态同步。

Redis Cluster总线的实现方式是基于UDP协议的广播机制。当一个节点需要向其他节点发送消息时，它会将消息广播到所有节点，每个节点收到消息后会判断该消息是否属于自己负责的槽位，如果是则进行相应的处理，如果不是则忽略该消息。

使用UDP协议的广播机制可以实现高效的消息传递，因为不需要建立连接和维护状态，每个节点都可以快速地将消息广播到其他节点。但是由于UDP协议的不可靠性，可能会出现消息丢失或者乱序的情况，因此Redis Cluster对总线的实现进行了一些优化和改进，比如使用消息序列号和消息重试机制等，以保证消息的可靠传递和正确处理。

总的来说，Redis Cluster总线是实现Redis Cluster分布式架构中节点间通信和状态同步的重要组成部分，它使用UDP协议的广播机制实现高效的消息传递，同时还对消息传递的可靠性进行了优化和改进，以保证系统的正确运行和数据的一致性。





## 为什么Redis Cluster的Hash Slot 是16384？

我们知道一致性hash算法是2的16次方，为什么hash slot是2的14次方呢？作者[原始回答在新窗口打开](https://github.com/redis/redis/issues/2576)

在redis节点发送心跳包时需要把所有的槽放到这个心跳包里，以便让节点知道当前集群信息，16384=16k，在发送心跳包时使用char进行bitmap压缩后是2k（2 * 8 (8 bit) * 1024(1k) = 16K），也就是说使用2k的空间创建了16k的槽数。

虽然使用CRC16算法最多可以分配65535（2^16-1）个槽位，65535=65k，压缩后就是8k（8 * 8 (8 bit) * 1024(1k) =65K），也就是说需要需要8k的心跳包，作者认为这样做不太值得；并且一般情况下一个redis集群不会有超过1000个master节点，所以16k的槽位是个比较合适的选择。





## 为什么Redis Cluster中不建议使用发布订阅呢？

在集群模式下，所有的publish命令都会向所有节点（包括从节点）进行广播，造成每条publish数据都会在集群内所有节点传播一次，加重了带宽负担，对于在有大量节点的集群中频繁使用pub，会严重消耗带宽，不建议使用。（虽然官网上讲有时候可以使用Bloom过滤器或其他算法进行优化的）









## 常规问题

- 什么是redis，为什么要使用它
- redis一般有哪些使用场景
- redis为什么快

## [#](#数据类型和数据结构) 数据类型和数据结构

- redis有哪些数据类型
- redis数据类型有哪些命令
- 谈谈redis的对象机制（redisObject)
- redis数据类型有哪些底层数据结构
- 为什么要设计sds？
- 一个字符串类型的值能存储最大容量是多少？512M
- 为什么会设计Stream
- Stream用在什么样场景
- 消息ID的设计是否考虑了时间回拨的问题

## [#](#持久化和内存) 持久化和内存

- Redis 的持久化机制是什么？各自的优缺点？一般怎么用？
- Redis 过期键的删除策略有哪些
- Redis 内存淘汰算法有哪些
- Redis的内存用完了会发生什么？ 如果达到设置的上限，Redis的写命令会返回错误信息（但是读命令还可以正常返回。）或者你可以配置内存淘汰机制，当Redis达到内存上限时会冲刷掉旧的内容。
- Redis如何做内存优化？
- Redis key 的过期时间和永久有效分别怎么设置？

EXPIRE 和 PERSIST 命令

- Redis 中的管道有什么用？

一次请求/响应服务器能实现处理新的请求即使旧的请求还未被响应，这样就可以将多个命令发送到服务器，而不用等待回复，最后在一个步骤中读取该答复。

这就是管道（pipelining），是一种几十年来广泛使用的技术。例如许多 POP3 协议已经实现支持这个功能，大大加快了从服务器下载新邮件的过程。

## [#](#事务) 事务

- 什么是redis事务
- Redis事务相关命令
- Redis事务的三个阶段
- watch是如何监视实现的呢
- 为什么 Redis 不支持回滚
- redis 对 ACID的支持性理解
- Redis事务其他实现

基于Lua脚本，Redis可以保证脚本内的命令一次性、按顺序地执行，其同时也不提供事务运行错误的回滚，执行过程中如果部分命令运行错误，剩下的命令还是会继续运行完

基于中间标记变量，通过另外的标记变量来标识事务是否执行完成，读取数据时先读取该标记变量判断是否事务执行完成。但这样会需要额外写代码实现，比较繁琐

## [#](#集群) 集群

### [#](#主从复制) 主从复制

- Redis集群的主从复制模型是怎样的？
- 全量复制的三个阶段？
- 为什么会设计增量复制？
- 增量复制的流程？ 如果在网络断开期间，repl_backlog_size环形缓冲区写满之后，从库是会丢失掉那部分被覆盖掉的数据，还是直接进行全量复制呢？
- 为什么不持久化的主服务器自动重启非常危险呢?
- 为什么主从全量复制使用RDB而不使用AOF？
- 为什么还有无磁盘复制模式？
- 为什么还会有从库的从库的设计？

### [#](#哨兵机制) 哨兵机制

- Redis哨兵机制？哨兵实现了什么功能呢
- 哨兵集群是通过什么方式组建的？
- 哨兵是如何监控Redis集群的？
- 哨兵如何判断主库已经下线了呢？
- 哨兵的选举机制是什么样的？
- Redis 1主4从，5个哨兵，哨兵配置quorum为2，如果3个哨兵故障，当主库宕机时，哨兵能否判断主库“客观下线”？能否自动切换？
- 主库判定客观下线了，那么如何从剩余的从库中选择一个新的主库呢？
- 新的主库选择出来后，如何进行故障的转移？

### [#](#redis集群) Redis集群

- 说说Redis哈希槽的概念？为什么是16384个？
- Redis集群会有写操作丢失吗？为什么？

Redis并不能保证数据的强一致性，这意味这在实际中集群在特定的条件下可能会丢失写操作。

## [#](#应用场景) 应用场景

- redis 客户端有哪些

Redisson、Jedis、lettuce等等，官方推荐使用Redisson。

Redisson是一个高级的分布式协调Redis客服端，能帮助用户在分布式环境中轻松实现一些Java的对象 (Bloom filter, BitSet, Set, SetMultimap, ScoredSortedSet, SortedSet, Map, ConcurrentMap, List, ListMultimap, Queue, BlockingQueue, Deque, BlockingDeque, Semaphore, Lock, ReadWriteLock, AtomicLong, CountDownLatch, Publish / Subscribe, HyperLogLog)。

- Redis如何做大量数据插入？ Redis2.6开始redis-cli支持一种新的被称之为pipe mode的新模式用于执行大量数据插入工作。
- redis实现分布式锁实现? 什么是 RedLock?
- redis缓存有哪些问题，如何解决
- redis和其它数据库一致性问题如何解决
- redis性能问题有哪些，如何分析定位解决

## [#](#新版本) 新版本

- Redis单线程模型？ 在6.0之前如何提高多核CPU的利用率？

可以在同一个服务器部署多个Redis的实例，并把他们当作不同的服务器来使用，在某些时候，无论如何一个服务器是不够的， 所以，如果你想使用多个CPU，你可以考虑一下分片（shard）。

- 6.0版本中多线程

------

著作权归@pdai所有 原文链接：https://pdai.tech/md/db/nosql-redis/db-redis-z-mianshi.html