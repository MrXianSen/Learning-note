# ClickHouse

## 源码编译

[ClickHouse源码编译 Mac OS X](https://clickhouse.com/docs/en/development/build-osx/)



## 副本&分片

### 副本

副本是为了保证数据的高可用，在分布式集群中存储的数据冗余。

### 分片（Shard）

副本解决了高可用问题，但是不能解决横向扩容问题，每台机器上实际容纳全部数据。

通过分片解决数据的水平切分问题。通过分片把一份完整的数据进行切分，不同的分片分布在不同的节点上。在通过分布式表引擎把数据拼接起来使用。

> 注意和**分区**区别开，分区是指表的分区，通过partition by语句让一个表的数据按照指定的分区规则写入到不同的数据目录中



## 表

Clickhouse分布式集群中，通常先创建本地表，再创建分布式表。本地表存储数据，分布式表只是一个查询引擎，本身不存储数据，查询时讲SQL发送到所有集群分片，然后进行处理和聚合，然后讲结果返回给客户端。

### 本地表

存储数据

### 分布式表

逻辑表，本身不存储数据，可以理解伟数据库中的视图，一般分布式表作为读表，分布式表引擎会将我们的查询请求路由到本地表进行查询，然后进行汇总最最终返回给用户。



## 数据存储

### 文件结构

clickhouse存储目录下一个数据列会存储一个文件

数据存储目录：

${home}/clickhouse/data/${db_name}/${table_name}/${partition}/

- columnName.bin

  列存文件数据

- columnName.mrk2

  每一列数据的列存数据标记

- columnName.null.bin

- columnName.null.mrk2

  这两个文件分别是对应的列值为null的数据

- checksum.txt

  当前目录下各个目录的带下和各文件内存的Hash，用于完整性校验

- count.txt

  当前分区中数据条数

- default_compression_codec.txt

  数据文件的默认压缩算法

- partition.dat

  从分区列计算出分区值得方法

- primary.idx

  数据索引，其实是排序键的那一列每间隔index_granularity的值，如果有n列，那每间隔index_granularity就会有n个值，同时也会受index_granularity_bytes影响

- ttl.txt

  > ttl format version: 1
  > {"table":{"min":1663141752,"max":1663143264}}

### 存储格式

- wide

  按列存，每一列存储一个文件

- compat

  所有数据回写入到一个文件，对于小批量数据写入更友好

- 索引

  clickhouse使用稀疏索引

- block

- 颗粒



## 源码

[ClickHouse源码分析](ClickHouse源码分析.md)

