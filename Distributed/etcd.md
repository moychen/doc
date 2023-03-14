# etcd手册

## introduce

​		**etcd** 是一个高度一致的分布式键值存储，它提供了一种可靠的方式来存储需要由分布式系统或机器集群访问的数据。它可以优雅地处理网络分区期间的领导者选举，即使在领导者节点中也可以容忍机器故障。etcd机器之间的通信通过Raft共识算法处理。

## Data model

etcd is designed to reliably store infrequently updated data and provide reliable watch queries. etcd exposes previous versions of key-value pairs to support inexpensive snapshots and watch history events (“time travel queries”). A persistent, multi-version, concurrency-control data model is a good fit for these use cases.

​		etcd 旨在可靠的存储那些不经常更新的数据并提供可靠的监控查询。etcd 暴露旧版本的键值对数据以支持廉价的快照和监控历史事件（“时间旅行查询”）。一个持久化、多版本、并发控制数据模型非常适合这些用例。

etcd stores data in a multiversion [persistent](https://en.wikipedia.org/wiki/Persistent_data_structure) key-value store. The persistent key-value store preserves the previous version of a key-value pair when its value is superseded with new data. The key-value store is effectively immutable; its operations do not update the structure in-place, but instead always generate a new updated structure. All past versions of keys are still accessible and watchable after modification. To prevent the data store from growing indefinitely over time and from maintaining old versions, the store may be compacted to shed the oldest versions of superseded data.

​		etcd 将数据存储在多版本持久化键值存储中。 当键值对的值被新数据取代时，持久化的键值存储会保留键值对的先前版本。 键值存储实际上是不可变的；它的操作不会就地更新结构，而是总是生成一个新的更新结构。 所有历史版本的 key 值在修改后仍然可以访问和监控。 为了防止数据存储随着时间的推移无限增长以及维护旧版本，可以压缩存储以摆脱被取代的数据的最旧版本。

### Logical view

The store’s logical view is a flat binary key space. The key space has a lexically sorted index on byte string keys so range queries are inexpensive.

该存储的逻辑视图是一个扁平的二进制键空间。键空间有一个字节字符串键的词法排序索引，所以范围查询是比较廉价的。

The key space maintains multiple **revisions**. When the store is created, the initial revision is 1. Each atomic mutative operation (e.g., a transaction operation may contain multiple operations) creates a new revision on the key space. All data held by previous revisions remains unchanged. Old versions of key can still be accessed through previous revisions. Likewise, revisions are indexed as well; ranging over revisions with watchers is efficient. If the store is compacted to save space, revisions before the compact revision will be removed. Revisions are monotonically increasing over the lifetime of a cluster.

​		键空间包含多个“修订版”，当存储被创建时，初始版本是1。每个原子突变操作（例如，一个事务操作可能包含多个操作）都会在键空间上创建一个新的修订版本。以前的修订版所持有的所有数据保持不变。键的旧版本仍然可以通过以前的修订版被访问。同样地，修订版也是有索引的；用观察者对修订版进行测算是有效的。如果存储被压缩以节省空间，在压缩修订之前的修订将被删除。修订版在集群的生命周期内是单调增长的。

A key’s life spans a generation, from creation to deletion. Each key may have one or multiple generations. Creating a key increments the **version** of that key, starting at 1 if the key does not exist at the current revision. Deleting a key generates a key tombstone, concluding the key’s current generation by resetting its version to 0. Each modification of a key increments its version; so, versions are monotonically increasing within a key’s generation. Once a compaction happens, any generation ended before the compaction revision will be removed, and values set before the compaction revision except the latest one will be removed.

### Physical view

etcd stores the physical data as key-value pairs in a persistent [b+tree](https://en.wikipedia.org/wiki/B%2B_tree). Each revision of the store’s state only contains the delta from its previous revision to be efficient. A single revision may correspond to multiple keys in the tree.

The key of key-value pair is a 3-tuple (major, sub, type). Major is the store revision holding the key. Sub differentiates among keys within the same revision. Type is an optional suffix for special value (e.g., `t` if the value contains a tombstone). The value of the key-value pair contains the modification from previous revision, thus one delta from previous revision. The b+tree is ordered by key in lexical byte-order. Ranged lookups over revision deltas are fast; this enables quickly finding modifications from one specific revision to another. Compaction removes out-of-date keys-value pairs.

etcd also keeps a secondary in-memory [btree](https://en.wikipedia.org/wiki/B-tree) index to speed up range queries over keys. The keys in the btree index are the keys of the store exposed to user. The value is a pointer to the modification of the persistent b+tree. Compaction removes dead pointers.

## etcd client design

## etcd learner design

## etcd v3 authentication design

## etcd versus other key-value stores

## Glossary

![image-20220503113908241](C:\Users\neighbor\AppData\Roaming\Typora\typora-user-images\image-20220503113908241.png)


