---
title: RocketMQ 学习
date: 2020-02-27 00:13:02
tags: 
    - RocketMQ
    - 消息队列
categorites: 中间件
---
# 消息队列

## 消息队列的用途

消息队列作为高并发系统的核心组件之一，能够帮助业务系统解构提升开发效率和系统稳定性。主要具有以下优势：

- 流量削峰（主要解决瞬时写压力大于应用服务能力导致消息丢失、系统奔溃等问题）
- 系统解耦（解决不同重要程度、不同能力级别系统之间依赖导致一死全死，将消息写入消息队列，需要消息的系统自己从消息队列中订阅）
- 异步处理（当存在一对多调用时，可以发一条消息给消息系统，让消息系统通知相关系统）
- 蓄流压测（线上有些链路不好压测，可以通过堆积一定量消息再放开来压测）
- 日志处理（将消息队列用在日志处理中，比如 Kafka 的应用，解决大量日志传输的问题）
- 消息通讯（消息队列一般都内置了高效的通信机制，因此也可以用在纯的消息通讯。比如实现点对点消息队列，或者聊天室等）

## 消息队列的选型

- Kafka 是 linkedin 开源的 MQ 系统，主要特点是基于 Pull 的模式来处理消息消费，追求高吞吐量，一开始的目的就是用于日志收集和传输，0.8 开始支持复制，不支持事务，适合产生大量数据的互联网服务的数据收集业务。
- RabbitMQ 是使用 Erlang 语言开发的开源消息队列系统，基于 AMQP 协议来实现。AMQP 的主要特征是面向消息、队列、路由（包括点对点和发布/订阅）、可靠性、安全。AMQP 协议更多用在企业系统内，对数据一致性、稳定性和可靠性要求很高的场景，对性能和吞吐量的要求还在其次。
- RocketMQ 是阿里开源的消息中间件，它是纯 Java 开发，具有高吞吐量、高可用性、适合大规模分布式系统应用的特点。RocketMQ 思路起源于 Kafka，但并不是 Kafka 的一个 Copy，它对消息的可靠传输及事务性做了优化，目前在阿里集团被广泛应用于交易、充值、流计算、消息推送、日志流式处理、binglog 分发等场景。

## RocketMQ 优势

目前主流的 MQ 主要是 RocketMQ、kafka、RabbitMQ，RocketMQ 相比于 RabbitMQ、kafka 具有主要优势特性有：
- 支持事务型消息（消息发送和 DB 操作保持两方的最终一致性，RabbitMQ、kafka 和 kafka 不支持）
- 支持结合 RocketMQ 的多个系统之间数据最终一致性（多方事务，二方事务是前提）
- 支持 18 个级别的延迟消息（RabbitMQ 和 kafka 不支持）
- 支持指定次数和时间间隔的失败消息重发（kafka 不支持，RabbitMQ 需要手动确认）
- 支持 consumer 端 tag 过滤，减少不必要的网络传输（RabbitMQ 和 kafka 不支持）
- 支持重复消费（RabbitMQ 不支持，kafka 支持）

# 消息中间件的组成

![](https://gitee.com/cellophane/image/raw/master/rocketmq.png)

整个 RocketMQ 消息系统主要由如下 4 个部分组成：

从中间件服务角度来看整个 RocketMQ 消息系统（服务端）主要分为：NameSrv 和 Broker 两个部分。

## Name Server
提供服务发现和注册，这里主要是管理 Broker，NameSrv 接受来自 Broker 的注册，并通过心跳机制来检测Broker服务的健康性。

提供路由功能，集群(这里是指以集群方式部署的 NameSrv)中的每个 NameSrv 都保存了 Broker 集群(这里是指以集群方式部署的 Broker)中整个的路由信息和队列信息。这里需要注意，在 NameSrv 集群中，每个 NameSrv 都是相互独立的，所以每个 Broker 需要连接所有的 NameSrv，每创建一个新的 topic  都要同步到所的 NameSrv 上。

Name Server 是一个几乎无状态节点，可集群部署，节点之间无任何信息同步。

## Broker
消息中转角色，负责消息的存储、传递、查询以及高可用(HA)保证等。其由如下几个子模块（源码总体上也是这么拆分的）构成：
- remoting：是 Broker 的服务入口，负责客户端的接入（Producer 和 Consumer）和请求处理。
- client：管理客户端和维护消费者对于 Topic 的订阅。
- store：提供针对存储和消息查询的简单的API（数据存储在物理磁盘）。
- HA：提供数据在主从节点间同步的功能特性。
- Index：通过特定的 key 构建消息索引，并提供快速的索引查询服务。

## Producer
消息的生产者，由用户进行分布式部署，消息由 Producer 通过多种负载均衡模式发送到 Broker 集群，发送低延时，支持快速失败。

支持生产者的 synchronous, asynchronous and one-way（同步，异步，单向发送）

## Consumer
消息的消费者，也由用户部署，支持 PUSH 和 PULL 两种消费模式，支持集群消费和广播消息，提供实时的消息订阅机制，满足大多数消费场景。

![信息流转](https://gitee.com/cellophane/image/raw/master/rmq-liucheng.jpg)

## Topic
主题，发布订阅模式下的消息统一汇集地，不同生产者向 topic 发送消息，由 MQ 服务器分发到不同的订阅者，实现消息的广播。

## Queue
队列，PTP 模式下，特定生产者向特定 queue 发送消息，消费者订阅特定的 queue 完成指定消息的接收

## Message
消息体，根据不同通信协议定义的固定格式进行编码的数据包，来封装业务数据，实现消息的传输

# 消息中间件模式分类

## 点对点
PTP 点对点:使用 queue 作为通信载体

消息生产者生产消息发送到 queue 中，然后消息消费者从 queue 中取出并且消费消息。

消息被消费以后，queue 中不再存储，所以消息消费者不可能消费到已经被消费的消息。Queue 支持存在多个消费者，但是对一个消息而言，只会有一个消费者可以消费。

## 发布/订阅
Pub/Sub 发布订阅（广播）：使用 topic 作为通信载体

消息生产者（发布）将消息发布到 topic 中，同时有多个消息消费者（订阅）消费该消息。和点对点方式不同，发布到 topic 的消息会被所有订阅者消费。

queue 实现了负载均衡，将 producer 生产的消息发送到消息队列中，由多个消费者消费。但一个消息只能被一个消费者接受，当没有消费者可用时，这个消息会被保存直到有一个可用的消费者。

topic 实现了发布和订阅，当你发布一个消息，所有订阅这个 topic 的服务都能得到这个消息，所以从 1 到 N 个订阅者都能得到一个消息的拷贝。

RocketMQ-nameSrv 用于管理所有 broker 的信息，以便于 Producer 和 Consumer 能够获取到正确的 Broker 信息，进行业务处理；

可以看到 NameSrv 的主要管理内容如下：

1. 接收 Broker 的注册，注销请求

2. Producer 获取 topic 下的所有 BrokerQueue，put 消息

3. Consumer 获取 topic 下所有的 BrokerQueue，get 消息

所以可以看到 NameSrv 主要是维护了 Broker 相关的内容，进行存取。

# 消息可靠性
1. Broker 异常 Crash
1. OS Crash
1. 机器掉电，但是能立即恢复供电情况。
1. 机器无法开机（可能是 cpu、主板、内存等关键设备损坏）
1. 磁盘设备损坏。

前面三种 RockerMQ 可以保证消息不丢，或者丢失少量数据（依赖刷盘方式是同步还是异步）。
后面两种属于单点故障，无法恢复。RocketMQ 在这两种情况下，通过异步复制，保证 99% 的消息不丢，同步双鞋可以完全避免单点，但是会影响性能。

# 回溯消费
回溯消费是指 Consumer 已经消费成功的消息，由于业务上需求需要重新消费，要支持此功能，Broker 在向 Consumer 投递成功消息后，消息仍然需要保留。并且重新消费一般是按照时间维度，例如由于 Consumer 系统故障，恢复后需要重新消费 1 小时前的数据，那么 Broker 要提供一种机制，可以按照时间维度来回退消费进度。
RocketMQ 支持按照时间回溯消费，时间维度精确到毫秒，可以向前回溯，也可以向后回溯。

# RocketMQ 集群模式
RocketMQ 集群部署有多种模式，对于 NameSrv 来说可以同时部署多个节点，并且这些节点间也不需要有任何的信息同步，这里因为每个 NameSrv 节点都会存储全量路由信息，在 NameSrv 集群模式下，每个 Broker 都需要同时向集群中的每个 NameSrv 节点发送注册信息，所以这里对于 NameSrv 的集群部署来说并不需要做什么额外的设置。

而对于Broker集群来说就有多种模式了，下面先绍下这几种模式，然后再来看看生产级的部署方式是什么
- 单个Master模式
一个 Broker 作为主服务，不设置任何 Slave，很显然这种方式风险比较大，存在单节点故障会导致整个基于消息的服务挂掉，所以生产环境不可能采用这种模式。
- 多 Master 模式
这种模式的 Broker 集群，全是 Master，没有 Slave 节点。这种方式的优缺点如下：

优点：配置会比较简单一些，如果单个 Master 挂掉或重启维护的话对应用是没有什么影响的。如果磁盘配置为 RAID10（服务器的磁盘阵列模式）的话，即使在机器宕机不可恢复的情况下，由于 RAID10 磁盘本身的可靠性，消息也不会丢失（异步刷盘丢失少量消息，同步刷盘一条不丢），这种 Broker 的集群模式性能相对来说是最高的。

缺点：就是在单台机器宕机期间，这台机器上未被消费的消息在机器恢复之前是不可以进行消息订阅的，这对消息的实时性会有一些影响。
- 多 Master 多 Slave 模式（异步复制）
在这种模式下 Broker 集群存在多个 Master 节点，并且每个 Master 节点都会对应一个 Slave 节点，有多对 Master-Slave，HA(高可用)之间采用异步复制的方式进行信息同步，在这种方式下主从之间会有短暂的毫秒级的消息延迟。

优点：在这种模式下即便磁盘损坏了，消息丢失的情况也非常少，因为主从之间有信息备份；并且，在这种模式下消息的实时性也不会受影响，因为 Master 宕机后 Slave 可以自动切换为 Master 模式，这样 Consumer 仍然可以通过Slave进行消息消费，而这个过程对应用来说则是完全透明的，并不需要人工干预。另外，这种模式的性能与多 Master 模式几乎差不多。

缺点：如果 Master 宕机，并且在磁盘损坏的情况下，会丢失少量的消息。

- 多 Master 多 Slave 模式（同步复制）
这种上一个模式差不多，只是 HA 采用的是同步双写的方式，即主备都写成功后，才会向应用返回成功。

优点：在这种模式下数据与服务都不存在单点的情况，在 Master 宕机的情况下，消息也没有延迟，服务的可用性以及数据的可用性都非常高。

缺点：性能相比于异步复制来说略低一些（大约10%）。另外一个缺点就是相比于异步复制，目前 Slave 备机还暂时不能实现自动切换为 Master，可能后续的版本会支持 Master-Slave 的自动切换功能。

综合考虑以上集群模式的优缺点，在实际生产环境中目前基于 RocketMQ 消息集群的部署方式基本都是采用多 Master 多 Slave（异步复制）这种模式。

## 使用了消息队列会有什么问题

- 系统可用性降低
- 系统复杂性增加，比如重复消费、消息丢失、消息的顺序消费。

## 如何保证高可用

用多 master 多 slave 异步复制模式集群。

通信过程如下：

Producer 与 NameServer 集群中的其中一个节点（随机选择）建立长连接，定期从 NameServer 获取 Topic 路由信息，并向提供 Topic 服务的 Broker Master 建立长连接，定时向 Broker 发送心跳。Producer 只能将消息发送到 Broker master，但是 Consumer 则不一样，它同时和提供 Topic 服务的 Master 和 Slave 建立长连接，既可以从 Broker Master 订阅消息，也可以从 Broker Slave 订阅消息。

# 启动源码分析
下面是一个很简单的服务端启动的两行源码
```java
MQProducer producer = new DefaultMQProducer("please_rename_unique_group_name");
producer.start();
```
启动源码的逻辑主要集中在start方法里面
```java
@Override
public void start() throws MQClientException {
    // 1. 设置生产组信息
    this.setProducerGroup(withNamespace(this.producerGroup));
    // 2. 重点，生产者进行启动
    this.defaultMQProducerImpl.start();
    if (null != traceDispatcher) {
        try {
            // 3. RocketMQ在4.4.0 Release 版本中支持了消息轨迹特性，这一特性的增加能够让我们对消息从生产到存储到消费这一整个流程有一个清晰的掌握，配合上 console1.0.1 以上版本的图形化界面，对于错误排查及日常运维是一个很有用处的 feature
            traceDispatcher.start(this.getNamesrvAddr(), this.getAccessChannel());
        } catch (MQClientException e) {
            log.warn("trace dispatcher start failed ", e);
        }
    }
}
```
DefaultMQProducerImpl start方法
```java
public void start() throws MQClientException {
    // 调用真正的初始化方法
    this.start(true);
}
```
真正的 start 方法上拥有这个 startFactory 参数 ， 一个 boolean 参数，用来标识是否启动 **mQClientFactory**

下面看一下这个方法
```java
/**
 * 生产者初始化
 *
 * @param startFactory 用来表示，是否初始化mQClientFactory
 * @throws MQClientException
 */
public void start(final boolean startFactory) throws MQClientException {
    // 首次调用该方法时serviceState = CREATE_JUST ， 等待创建
    switch (this.serviceState) {
        case CREATE_JUST:
            // 1. 一进来，就默认状态为失败，主要是为了防止重复执行
            this.serviceState = ServiceState.START_FAILED;
            // 2. 检查生产组名称配置
            this.checkConfig();
            // 3. 如果生产组名称不等于 CLIENT_INNER_PRODUCER ， 则需要吧instanceName修改一下
            if (!this.defaultMQProducer.getProducerGroup().
                    equals(MixAll.CLIENT_INNER_PRODUCER_GROUP)) {
                this.defaultMQProducer.changeInstanceNameToPID();
            }
            // 4. 创建一个和broker交互的MQ实例
            this.mQClientFactory = MQClientManager.getInstance().getAndCreateMQClientInstance(
                    this.defaultMQProducer, rpcHook);
            // 5. 注册生产组信息，到本地的MAP里面去
            boolean registerOK = mQClientFactory.registerProducer(
                    this.defaultMQProducer.getProducerGroup(), this);
            if (!registerOK) {
                // 如果注册不成功，那么需要把服务的启动状态设置为CREATE_JUST，
                this.serviceState = ServiceState.CREATE_JUST;
                throw new MQClientException("The producer group[" + this.defaultMQProducer.
                        getProducerGroup() + "] has been created before, specify another name please." + FAQUrl.suggestTodo(FAQUrl.GROUP_NAME_DUPLICATE_URL), null);
            }
            // 生产者启动是，默认的 topic = TBW102 , 放入到本地的topic的ConcurrentHashMap中
            this.topicPublishInfoTable.put(this.defaultMQProducer.getCreateTopicKey(), new TopicPublishInfo());
            // 启动一个生产组时，只有一次startFactory = true, 因为在mQClientFactory.start();方法中也会调用start方法，只不过startFactory = false
            if (startFactory) {
                // 6. 启动MQ实例
                mQClientFactory.start();
            }

            log.info("the producer [{}] start OK. sendMessageWithVIPChannel={}", this.defaultMQProducer.getProducerGroup(),
                    this.defaultMQProducer.isSendMessageWithVIPChannel());
            // 启动成功
            this.serviceState = ServiceState.RUNNING;
            break;
        case RUNNING:
        case START_FAILED:
        case SHUTDOWN_ALREADY:
            // 正在运行， 启动失败，准备关闭，这3个状态下，如果还有人调用start方法，那么就会直接报错了
            throw new MQClientException("The producer service state not OK, maybe started once, "
                    + this.serviceState
                    + FAQUrl.suggestTodo(FAQUrl.CLIENT_SERVICE_NOT_OK),
                    null);
        default:
            break;
    }
    // 7. 设置发送心跳给broker
    this.mQClientFactory.sendHeartbeatToAllBrokerWithLock();
}
```
在一开始的状态量为 CREAT_JUST,可以顺利进入具体的开始实现，同时立马将状态修改为失败，主要是为了防止重复执行

对生产者的 group 名称做校验
```java
private void checkConfig() throws MQClientException {
    Validators.checkGroup(this.defaultMQProducer.getProducerGroup());
    // 生产组名称不能为空
    if (null == this.defaultMQProducer.getProducerGroup()) {
        throw new MQClientException("producerGroup is null", null);
    }
    // 生产组名称不能 等于 DEFAULT_PRODUCER
    if (this.defaultMQProducer.getProducerGroup().equals(MixAll.
            DEFAULT_PRODUCER_GROUP)) {
        throw new MQClientException("producerGroup can not equal " + MixAll.DEFAULT_PRODUCER_GROUP + ", please specify another one.", null);
    }
}
```
如果生产组名称不等于 CLIENT_INNER_PRODUCER （mQClientFactory作为和broker交互的实例里面初始化的名称就是这个） ， 则需要把instanceName修改一下 ，因为, 执行changeInstanceNameToPID方法
```java
//instanceName如果不设置的话就是DEFAULT
private String instanceName = System.getProperty("rocketmq.client.name", "DEFAULT");
public void changeInstanceNameToPID() {
    if (this.instanceName.equals("DEFAULT")) {
        // 更新DEFAULT = 当前JVM的进程PID
        this.instanceName = String.valueOf(UtilAll.getPid());
    }
}
```
创建一个和broker交互的MQ实例 ， 通过MQClientManager这个类创建MQClient
```java
public MQClientInstance getAndCreateMQClientInstance(final ClientConfig clientConfig, RPCHook rpcHook) {
    // 构建MQClientId， 这个ID很重要，在MQ多个集群的时候，这个如果不注意会出现问题。
    String clientId = clientConfig.buildMQClientId();
    // 通过clientId从factoryTable中获取是否已经被创建过，如果已经被创建过，则直接复用
    MQClientInstance instance = this.factoryTable.get(clientId);
    if (null == instance) {
        // 为空，则继续创建MQClientInstance ，
        instance =new MQClientInstance(clientConfig.cloneClientConfig(),
                this.factoryIndexGenerator.getAndIncrement(), clientId, rpcHook);
        MQClientInstance prev = this.factoryTable.putIfAbsent(clientId, instance);
        if (prev != null) {
            instance = prev;
            log.warn("Returned Previous MQClientInstance for clientId:[{}]", clientId);
        } else {
            log.info("Created new MQClientInstance for clientId:[{}]", clientId);
        }
    }
    return instance;
}
// org.apache.rocketmq.client.ClientConfig
public String buildMQClientId() {
    StringBuilder sb = new StringBuilder();
    // 本机IP
    sb.append(this.getClientIP());
    sb.append("@");
    // 实例名称
    sb.append(this.getInstanceName());
    if (!UtilAll.isBlank(this.unitName)) {
        sb.append("@");
        sb.append(this.unitName);
    }
    return sb.toString();
}
```
注册生产组信息，到本地的 MAP 里面去，启动 MQ 实例 ，MQClientInstance.start 方法
```java
public void start() throws MQClientException {
    synchronized (this) {
        switch (this.serviceState) {
            case CREATE_JUST:
                this.serviceState = ServiceState.START_FAILED;
                // If not specified,looking address from name server
                if (null == this.clientConfig.getNamesrvAddr()) {
                    this.mQClientAPIImpl.fetchNameServerAddr();
                }
                // 负责网络通信的接口调用启动
                this.mQClientAPIImpl.start();
                // 初始化各类定时任务
                this.startScheduledTask();
                // 开启pullService , 针对consumer，到讲解consumer时再做详细说明
                this.pullMessageService.start();
                // 启动RebalanceService服务，针对consumer，到讲解consumer时再做详细说明
                this.rebalanceService.start();
                // 启动一个groupName为CLIENT_INNER_PRODUCER的DefaultMQProducer，
                // 用于将消费失败的消息发回broker,消息的topic格式为%RETRY%ConsumerGroupName。
                this.defaultMQProducer.getDefaultMQProducerImpl().start(false);
                log.info("the client factory [{}] start OK", this.clientId);
                this.serviceState = ServiceState.RUNNING;
                break;
            case RUNNING:
                break;
            case SHUTDOWN_ALREADY:
                break;
            case START_FAILED:
                throw new MQClientException("The Factory object[" + this.getClientId() + "] has been created before, and failed.", null);
            default:
                break;
        }
    }
}
```

# 问题定位方法
什么情况下需要查看 mq 的 log 定位问题：当出现 mq 消费丢失时。

如贷出时，产品系统更新产品数据成功后，是通过发送通知消息到 mq，再由 mq 推送消费请求到列表系统，列表系统写入数据库，我们才能在借入列表页看到这个待借标的的数据更新。如果对一个标贷出之后经过了一段时间（不应超过几分钟）仍旧无法在列表页看到该标的相关变化（前提要确保页面确实刷新且返回了数据），而产品系统已成功入库更新了该产品，我们就可通过产品系统日志查看最后是否有推送相关 topic 给 mq。如果日志显示推送了且 mq 返回成功，就有很大可能是 mq 消费丢失导致了列表系统没有获取到更新信息，此时如果列表系统没有相关日志如消费处理失败等记录，且数据库没有更新，那基本可以确定就是 mq 消费过程中出现了丢失。

为什么会出现 mq 消费丢失:mq 同其他系统间调用一样有可能出现各种情况的调用失败，因此我们需要通过查看 mq 日志定位具体问题。
- 最常见的，容器组内没有拉 RocketMQ 容器，导致错误消费。
- 如容器组内只有消费者，则消费者（前例中的列表系统）会注册到基准的 mq，但消费者没有外网，或代码有问题／不够新（不是 master 分支或很久没有重启更新代码），基准 mq 随机选择一个消费者推送时有一定可能会推送到该机器，此时消费失败。
- 如容器组内只有生产者，则生产者（前例中的产品系统）会推送到基准的 mq，而基准的消费者（前例中的列表系统）没有新的代码去消费该 mq。
- 配置错误，如 mq 没有回调到消费者正确的路径
- 环境问题，如 mq 容器启动失败
- 网络问题，如 mq 和消费者之间网络不同导致调用失败等

## RocketMQ 基本配置：
RocketMQ 虽然包含很多知识点，但我们 debug 时主要关注 `nameserver`（负责关联请求中topic和消费者的关系）和 `broker`（负责处理具体的消费）。业务系统在启动后，会通过自己系统内的配置找到 mq 的 `nameserver` 得到 `broker` 的地址（然后针对 java 的业务系统会跟 `broker` 建立长链接，针对 php 的业务系统 mq 会将该对应关系保存在数据）。`nameserver` 上会记录一个 `topic` 已经有多少个消费者连接了，`broker` 上会记录最后是哪个消费者消费了一个具体的信息。

如何查看 `topic` 的注册消费者：
- 登陆 nameserver 机器，cd 到命令目录

```sh
sh mqadmin topicList -n 100.73.41.56:9876 -c |grep T_CONTRACT_EVENT（topic名字）
sh mqadmin consumerConnection -n 100.73.41.56:9876 -g S_JIAOYI_CG_T_JDB_CONTRACT_EVENT（上个步骤搜到的）
```
如何查看某个消息具体被谁（哪个容器）消费了：
在业务系统 log 里找到该 topic 对应的 msgIG，如：topic:T_CONTRACT_EVENT, key:T_CONTRACT_EVENT_661321962570698890, orderID:661321962570698891, msgID:6449293900002A9F000000001564A2B4。

查看 mq 日志：登陆 broker 机器，cd 到日志目录，grep msgID pullmsgLive.log
消费方式的区别：消费者是 php 的系统是 mq 回调消费者（dba 在 mqagent 配置一个回调路径），消费者是 java 的系统是主动去 mq 消费（轮询）。暂时都是这样的，以后可能会改。

# 参考文章
- [Rocketmq原理&最佳实践](https://www.jianshu.com/p/2838890f3284)
- [《RocketMq》三、NameServer](http://blog.csdn.net/xxxxxx91116/article/details/50353146 )
- [【分布式事务】基于RocketMQ搭建生产级消息集群？](https://mp.weixin.qq.com/s?__biz=MzU3NDY4NzQwNQ==&mid=2247484461&idx=1&sn=72794d12ffe3c8ec3520fdba1c87c3b3&chksm=fd2fd5efca585cf955d240bbabd99b24262d255a3880454297f10a84e4c38445bc7b9d3ba339&scene=21#wechat_redirect)
- [十分钟入门RocketMQ](http://jm.taobao.org/2017/01/12/rocketmq-quick-start-in-10-minutes/)
- [【原创】分布式之消息队列复习精讲](https://www.cnblogs.com/rjzheng/p/8994962.html)
- [MQ消息队列最全总结！](https://mp.weixin.qq.com/s?__biz=MzU3NDY4NzQwNQ==&mid=2247483875&idx=1&sn=f26f56f444b219825ddd6f85c2335855&chksm=fd2fd021ca585937da76a775aadbb01c4f542bd009000adebc732716a677c7d45fc69e7008c7&scene=21#wechat_redirect)
- [【原创】分布式之消息队列复习精讲](https://www.cnblogs.com/rjzheng/p/8994962.html)

