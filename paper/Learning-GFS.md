# GFS

## 假设

- 构建在廉价的机器上；容错，高可靠，自愈等能力
- 存储上百万的文件，文件大小通常在100M左右，大文件友好；能支持小文件，但是性能不好
- 读写能力，顺序读写和随机读写
- 多用户并发
- High sustained bandwidth is more important than low latency。

## 架构

Single Master：

Multiple chunkservers

文件被分成固定带下的块，每个块有一个64位的编码和块创建时间

高可靠：每个块以副本的形式分布在多个节点上

Master节点上管理元数据（metadata）

## 元数据：

- 命名空间

- 文件和数据块的位置映射

  Master和chunkserver之间通过心跳来获取每个数据块的位置，在chunkserver启动的时候master会要求chunkserver把它上面的数据块位置信息通过心跳发送到Master

- 每个数据库副本所在的位置

- 操作日志

  操作日志是GFS的核心

  作为元数据的持久化文件

  保证GFS中所有的操作的逻辑一致性

  checkpoint，定期进行checkpoint存储GFS全局镜像

  

## 一致性模型



## 系统交互

### 租约（lease）

通过租约来保证数据写入的一致性

master给其中一个数据块颁发一个租约（chunk lease），把这个数据块叫做主Chunk

### 数据流（Data Flow）

为了提高网络效率，数据流和控制流分开

控制流的顺序：主Chunk服务器 -> 其它二级副本所在的服务器

数据流：暗战Chunk服务器线性推送数据

为了避免网络瓶颈以及保证低延时，从网络拓扑中选择没有接收过数据且离自己最近的Chunk服务器接收数据



## 快照（Snapshot）

集群中某个时刻的数据镜像



## Master

### namespace和锁

读锁和写锁



### 副本放置的位置

目标：最大化数据的可靠性和可用性，最大化网络带宽利用率。













