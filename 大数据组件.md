# Zookeeper

## 1、简介

zookeeper是一个开源的为分布式应用提供协调服务的Apache项目。

![image-20201214114410824](zookeeper.assets/image-20201214114410824.png)

1. Zookeeper：一个领导者（Leader），多个跟随者（Follower）组成的集群。
2. 集群中只要有半数以上节点存活，Zookeeper集群就能正常服务。
3. 全局数据一致：每个Server保存一份相同的数据副本，Client无论连接到哪个Server，数据都是一致的。
4. 更新请求顺序进行，来自同一个Client的更新请求按其发送顺序依次执行。
5. 数据更新原子性，一次数据更新要么成功，要么失败。
6. 实时性，在一定时间范围内，Client能读到最新数据。



ZooKeeper数据模型的结构与Unix文件系统很类似，整体上可以看作是一棵树，每个节点称做一个ZNode。每一个ZNode默认能够存储1MB的数据，每个ZNode都可以通过其路径唯一标识。

<img src="zookeeper.assets/image-20201214114653523.png" alt="image-20201214114653523" style="zoom:50%;" />

提供的服务包括：**统一命名服务、统一配置管理、统一集群管理、服务器节点动态上下线、软负载均衡等**。

## 2、节点类型

持久（Persistent）：客户端和服务器端断开连接后，创建的节点不删除

短暂（Ephemeral）：客户端和服务器端断开连接后，创建的节点自己删除

![image-20201214115644690](zookeeper.assets/image-20201214115644690.png)

## 3、监听器原理

1）首先要有一个main()线程

2）在main线程中创建Zookeeper客户端，这时就会创建两个线程，一个负责网络连接通信（connet），一个负责监听（listener）。

3）通过connect线程将注册的监听事件发送给Zookeeper。

4）在Zookeeper的注册监听器列表中将注册的监听事件添加到列表中。

5）Zookeeper监听到有数据或路径变化，就会将这个消息发送给listener线程。

6）listener线程内部调用了process()方法。

常见的监听：

1）监听节点数据的变化 get path [watch] 

2）监听子节点增减的变化  ls path [watch]

![image-20201214115933831](zookeeper.assets/image-20201214115933831.png)

## 4、Paxos算法

Paxos算法一种基于消息传递且具有高度容错特性的一致性算法。

分布式系统中的节点通信存在两种模型：共享内存（Shared memory）和消息传递（Messages passing）。基于消息传递通信模型的分布式系统，不可避免的会发生以下错误：进程可能会慢、被杀死或者重启，消息可能会延迟、丢失、重复，在基础 Paxos 场景中，先不考虑可能出现消息篡改即拜占庭错误的情况。Paxos 算法解决的问题是在一个可能发生上述异常的分布式系统中如何就某个值达成一致，保证不论发生以上任何异常，都不会破坏决议的一致性。

**Paxos算法描述：**

在一个Paxos系统中，首先将所有节点划分为Proposers，Acceptors，和Learners。（注意：每个节点都可以身兼数职）。

![image-20201214120414310](zookeeper.assets/image-20201214120414310.png)

一个完整的Paxos算法流程分为三个阶段：

- Prepare阶段

  Proposer向Acceptors发出Prepare请求Promise（承诺）

  Acceptors针对收到的Prepare请求进行Promise承诺

- Accept阶段1

  Proposer收到多数Acceptors承诺的Promise后，向Acceptors发出Propose请求

  Acceptors针对收到的Propose请求进行Accept处理

- Learn阶段：Proposer将形成的决议发送给所有Learners

## 5、选举机制

（1）半数机制：集群中半数以上机器存活，集群可用。所以Zookeeper适合安装奇数台服务器。

（2）Zookeeper虽然在配置文件中并没有指定Master和Slave。但是，Zookeeper工作时，是有一个节点为Leader，其他则为Follower，Leader是通过内部的选举机制临时产生的。

![image-20201214120823755](zookeeper.assets/image-20201214120823755.png)