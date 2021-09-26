## 引言

我从毕业参加工作开始接触大数据领域的相关技术，最开始是在大数据的平台上基于大数据进行产品开发，后面因为产品业务的调整，我们团队自己引入了产品需要的各种大数据组建。

我有幸参与其中，从选型、源码构建、环境调测以及后面必要的安全加固和产品定制，都端到端的参与到里面讨论、开发、填坑等等。



## 大数据领域理论基础

1. [100 open source Big Data architecture papers](https://www.linkedin.com/pulse/100-open-source-big-data-architecture-papers-anil-madan/)
2. [GFS论文](http://static.googleusercontent.com/media/research.google.com/en/us/archive/gfs-sosp2003.pdf)：网上同时也有很多翻译

## 大数据领域知识地图

### 大数据技术

#### 分布式系统

##### 可靠性

数据可靠目前最经典的解决方案是：数据副本，在集群中复制多份数据存储在不同的节点中，由此也就衍生除了多种数据可靠性的架构

- 主从架构：典型代表有GFS/Bigtable

  同步复制：Backup Master，

  异步复制：Shadow Master，sharding节点只能用于读

- 多主结构：Spanner

  跨数据中心/全球数据库

- 无主架构：Dynamo/Cassandra

  最终一致性 AP架构

##### 可扩展性

##### 可维护性

#### 存储引擎

#### 计算引擎