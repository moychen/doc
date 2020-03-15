# ZooKeeper手册

## 简介

Zoo

ZK 服务有两种模式（standalone和quorum），standalone模式下ZK集群只有一个独立运行的ZK节点，quorum模式下ZK集群包含多个ZK节点。应用使用ZK客户端库来使用ZK服务，ZK客户端负责和ZK集群交互。

![image-20200316011624971](F:\Doc\Distributed\images\Zookeeper\image-20200316011624971.png)

**session**





ZooKeeper适用于存储和协同相关的关键数据，不适用于大量数据存储。

**典型应用场景：**

> * 配置管理（Configuration Management）
> * DNS服务
> * 组成员管理（Group Membership）
> * 分布式锁



## 1. 基础篇

### 1.1 数据模型

​		ZooKeeper的数据模型是层次模型，层次模型常见于文件系统。ZooKeeper使用层次模型主要是基于以下两点考虑。

> * 文件系统的树形结构便于表达数据之间的层次关系；
> * 文件系统的树形结构便于为不同的应用分配独立的命名空间（namespace）。

​		ZooKeeper的层次模型称作 data tree。Data tree的每个节点称为znode。不同于文件系统，ZooKeeper的每个节点都可以保存数据。每个节点都有一个版本号（version），版本从0开始计数。

![image-20200315211414646](F:\Doc\Distributed\images\Zookeeper\image-20200315211414646.png)

​		ZooKeeper 对外提供一个用来访问 data tree 的简化文件系统API：

> * 使用 UNIX 风格的路径名来定位znode，例如 /A/X 表示 znode A 的子节点 X；
> * znode 的数据只支持全量写入和读取，没有像通用文件系统那样支持部分写入和读取；
> * data tree 的所有 API 都是 wait-free 的。正在执行的 API 调用不会影响其他 API 的完成；
> * data tree 的 API 都是对文件系统的 wait-free 操作，不直接提供锁这样的分布式协同机制。但是 data tree 的 API 非常强大，可以用来实现多种分布式协同机制。



### 1.2 znode分类

一个znode可以是持久性的，也可以是临时性的：

> * 持久性znode（PERSISTENT）：创建之后即使发生ZooKeeper集群宕机或者client宕机也不会丢失；
> * 临时性znode（EPHEMERAL）：client宕机或者client在指定的timeout时间内没有给ZooKeeper集群发送消息，则临时节点（同一会话内，待确认）就会消失。

​		znode节点也可以是顺序性的。每一个顺序性的znode关联一个唯一的单调递增整数。这个单调递增整数是znode名字的后缀。

> * 持久顺序性znode（PERSISTENT_SEQUENTIAL）：持久性znode具有顺序性。
> * 临时顺序性znode（EPHEMERAL_SEQUENtIAL）：临时性znode具有顺序性。

ZooKeeper主要有以上4种znode。其他暂不了解。



### 1.3 配置说明

参考示例配置文件：



### 1.4 常用命令

| 命令   | 说明                                                         |
| ------ | :----------------------------------------------------------- |
| help   | 查看命令及相关说明。                                         |
| ls     | 显示指定路径下的节点信息。                                                                                                                                                        ls -R /                    显示所有节点信息， -R表示递归                                                                                                                                  ls -w /workers     获取/workers下的节点并监控/workers下的节点变化，-w 表示watch |
| create | 创建节点，默认持久性           -e：表示创建临时节点          |
| stat   |                                                              |
|        |                                                              |
|        |                                                              |
| quit   |                                                              |


### 1.5 应用实例

#### master - worker协同

master-worker架构中有一个master负责监控worker的状态，并为worker分配任务。该架构要求；

> * 任何时刻系统中最多只能有一个master，不可能出现两个master的情况，多个master共存会导致脑裂（参考后面说明）。
> * 系统中除了处于active状态的master之外，还有备用master，如果主master异常，备master能很快切换为主master工作。
> * master实时监控worker的状态，能够及时收到worker成员变化的通知。master在收到worker成员变化的通知时，通常重新进行任务的重新分配。

**实现思路**

> * 使用一个临时节点/master表示主节点master，master在行使职能之前，首先要创建/master znode.如果能创建成功，则进入active状态，开始行使master职能。否则的话，进入backup状态，使用watch机制监控/master节点。假设系统中有主备两个master，如果主master异常，则创建的临时节点/master也会被ZK自动删除。此时备master会收到通知，通过再次创建/master临时节点称为新的主master。
> * worker通过在/workers下面创建临时节点来加入集群。
> * 处于active状态的master会通过watch机制监控/workers下的znode变化来实时获取worder成员的变化。



准备工作:

```bash
# 查看ZK现有节点信息
ls -R /

# 创建/workers节点
create /workers
```



Client1（Master1）:

```bash
# 尝试创建临时节点/master，创建成功进入Active状态
create -e /master "m1:2223"  

# 获取并监控/workers下的节点
ls -w /workers

# /wokers下节点变化，Master收到通知，重新获取并监控/workers下的节点
ls -w /workers
```

Client2（Master2）:

```bash
# 尝试创建临时节点/master，创建失败进入Backup状态，因为master1已创建
create -e /master "m1:2223"  

# 监控/master节点的变化,如果Master1异常，/master被ZK自动删除，Master2能监控到异常，然后再尝试创建/master，成功之后转为Active master。
stat -w /master
```



#### 分布式锁

Client1:

```bash
# 

```

Client2:

```bash
# 
```



### 1.6 启动检查

``` bash
# 启动zookeeper服务
zkServer.sh

# 日志路径下，查看是否存在异常或错误日志
grep -E -i "((exception)|(error))" *

# 检查端口是否在指定端口监听
netstat -an | ag 2181
```

## 2. 开发篇

## 3. 运维篇

## 4. 进阶篇

## 5.对比Chubby、etcd和ZooKeeper

## 6. ZooKeeper实现原理和源码解读

