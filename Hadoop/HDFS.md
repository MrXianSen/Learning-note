## HDFS

HDFS核心组件

### Namenode

- 元数据管理
- 负责接收和处理客户端请求

### JournalNode

- HA场景下，NameNode采用active-standby主备双活的方式部署，为了保证数据一致性，JournaNode在每次操作之后将该操作同步到standby节点

### ZKFC

- HA场景下，NameNode采用主备双活的方式部署，一个事件集群中namenode只有一个active节点，ZKFC可以用来控制namenode主备状态

### Datanode

- 存储和管理真实的数据，以block的方式存储在磁盘中

