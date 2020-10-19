



```bash
# 设置API 版本
$ set ETCDCTL_API=3
$ etcdctl help
```

```bash
# windows下执行命令前+start/b 让程序后台运行
# 启动zk和kafka服务端
$ start /b zookeeper-server-start.bat ..\..\config\zookeeper.properties
$ start /b kafka-server-start.bat ..\..\config\server.properties

# 创建一个主题
$kafka-topics.bat --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic kafka-test-topic
# 查看创建的主题列表
$ kafka-topics.bat --list --zookeeper localhost:2181

# 启动生产者：
$ kafka-console-producer.bat --broker-list localhost:9092 --topic kafka-test-topic
# 启动消费者：
$ kafka-console-consumer.bat --bootstrap-server localhost:9092 --topic kafka-test-topic --from-beginning
```

