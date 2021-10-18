参考[ClickHouse源码导读](http://sineyuan.github.io/post/clickhouse-source-guide/)

## Server

programs/server/Server.h

```c++
/** Server provides three interfaces:
  * 1. HTTP - simple interface for any applications.
  * 2. TCP - interface for native clickhouse-client and for server to server internal communications.
  *    More rich and efficient, but less compatible
  *     - data is transferred by columns;
  *     - data is transferred compressed;
  *    Allows to get more information in response.
  * 3. Interserver HTTP - for replication.
  */
```

Server短提供三种网络接口

- HTTP：处理任意应用请求解耦
- TCP：ch-client和ch-server内部通信接口
- Iterserver HTTP：处理数据副本复制



### main方法

**1、注册相关组件**

```c++
    registerFunctions();
    registerAggregateFunctions();
    registerTableFunctions();
    registerStorages();
    registerDictionaries();
    registerDisks();
    registerFormats();
```

**2、初始化线程池**

```c++
    // Initialize global thread pool. Do it before we fetch configs from zookeeper
    // nodes (`from_zk`), because ZooKeeper interface uses the pool. We will
    // ignore `max_thread_pool_size` in configs we fetch from ZK, but oh well.
    GlobalThreadPool::initialize(config().getUInt("max_thread_pool_size", 10000));
```

**3、？？？**

```c++
    ConnectionCollector::init(global_context, config().getUInt("max_threads_for_connection_collector", 10));
```

**4、初始化ZK连接**

```c++
    bool has_zookeeper = config().has("zookeeper");

    zkutil::ZooKeeperNodeCache main_config_zk_node_cache([&] { return global_context->getZooKeeper(); });
    zkutil::EventPtr main_config_zk_changed_event = std::make_shared<Poco::Event>();
    if (loaded_config.has_zk_includes)
    {
        auto old_configuration = loaded_config.configuration;
        ConfigProcessor config_processor(config_path);
        loaded_config = config_processor.loadConfigWithZooKeeperIncludes(
            main_config_zk_node_cache, main_config_zk_changed_event, /* fallback_to_preprocessed = */ true);
        config_processor.savePreprocessedConfig(loaded_config, config().getString("path", DBMS_DEFAULT_PATH));
        config().removeConfiguration(old_configuration.get());
        config().add(loaded_config.configuration.duplicate(), PRIO_DEFAULT, false);
    }

    Settings::checkNoSettingNamesAtTopLevel(config(), config_path);
```

**5、中间很长一串用来初始化执行环境已经执行配置**

TODO

**6、启动服务端的一些监听**

- http_port

- https_port

- tcp_port

- tcp_with_porxy_port：TCP with PROXY protocol

  https://github.com/wolfeidau/proxyv2/blob/master/docs/proxy-protocol.txt

- tcp_port_secure：TCP with SSL

- interserver_http_port

- interserver_https_port

- mysql_port

- postgresql_port

- grpc_port

- promethus.port

**7、加载配置**

比如分布式表支持相关的配置

```c++
        if (has_zookeeper && config().has("distributed_ddl"))
        {
            /// DDL worker should be started after all tables were loaded
            String ddl_zookeeper_path = config().getString("distributed_ddl.path", "/clickhouse/task_queue/ddl/");
            int pool_size = config().getInt("distributed_ddl.pool_size", 1);
            if (pool_size < 1)
                throw Exception("distributed_ddl.pool_size should be greater then 0", ErrorCodes::ARGUMENT_OUT_OF_BOUND);
            global_context->setDDLWorker(std::make_unique<DDLWorker>(pool_size, ddl_zookeeper_path, global_context, &config(),
                                                                     "distributed_ddl", "DDLWorker",
                                                                     &CurrentMetrics::MaxDDLEntryID, &CurrentMetrics::MaxPushedDDLEntryID));
        }

```



### 上下文

在Server.main中有这样一段代码

```c++
    /** Context contains all that query execution is dependent:
      *  settings, available functions, data types, aggregate functions, databases, ...
      */
    auto shared_context = Context::createShared();
    global_context = Context::createGlobal(shared_context.get());

    global_context->makeGlobalContext();
    global_context->setApplicationType(Context::ApplicationType::SERVER);
```

源码路径：src/Interpreters/Contxt.cpp

