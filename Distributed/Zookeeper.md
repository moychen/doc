# ZooKeeper手册

ZooKeeper适用于存储和协同相关的关键数据，不适用于大量数据存储。

**典型应用场景：**

> * 配置管理（Configuration Management）
> * DNS服务
> * 组成员管理（Group Membership）
> * 分布式锁

## 1. ZooKeeper基础

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

**help**

查看命令及相关说明。

**ls**

显示指定路径下的节点信息。

> * ls -R / 显示所有节点信息， -R表示递归

**create**

创建节点



``` bash
# 日志路径下查看是否存在异常或错误日志
grep -E -i "((exception)|(error))" *

# 检查端口是否在指定端口监听
netstat -an | ag 2181
```

