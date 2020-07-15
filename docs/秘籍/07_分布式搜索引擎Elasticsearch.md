## 分布式搜索引擎 Elasticsearch

一般业内的标准都是使用分布式搜索引擎 Elasticsearch，简称 es。在鑫课堂项目中就使用到了 es 来进行课程信息的检索。

### 0.es的基础知识

Elasticsearch 是一个基于[Lucene](https://baike.baidu.com/item/Lucene/6753302)的搜索服务器。它提供了一个分布式多用户能力的全文搜索引擎，基于RESTful web接口。

ES 的索引库是一个逻辑概念，它包括了**分词列表**及**文档列表**，同一个索引库中存储了相同类型的文档。它就相当于 MySQL中的表，或相当于 Mongodb 中的集合。

下图是 Elasticsearch 的索引结构，下边黑色部分是物理结构，上边黄色部分是逻辑结构，逻辑结构也是为了更好的去描述 Elasticsearch 的工作原理及去使用物理结构中的索引文件。


![1](https://wangxin1248.github.io/assets/images/2020/2020-04/1.png)

逻辑结构部分是一个**倒排索引表**：

- 将要搜索的文档内容分词，所有不重复的词组成分词列表。
- 将搜索的文档最终以Document方式存储起来。
- 每个词和docment都有关联。

所谓倒排索引表是正排索引表的相反操作，在正排索引表当中，索引查找的顺序在先找 document 文档，然后再从 document 文档中查找对应的 term 词；而倒排索引表刚好相反，索引先找 term 词，再根据 term 找对应的 document。因此倒排索引表需要专门保存 term 和 document 之间的对应关系的。

### 1.es的分布式架构原理能说一下吗？（es是如何实现分布式的？）

elasticsearch 的设计理念是分布式搜索引擎，底层其实还是基于 lucene 的。

核心思想就是在多台机器上启动多个 es 进程实例，组成一个 es 集群。es 中存储的基本单位是索引，索引就相当于 mysql 中的一张表。es 中的基本概念：index、type、mapping、document、field。（es在版本之后忽略type）mapping就是相当于 mysql 中的表结构定义，定义了type中每个字段的名称以及字段类型以及相应的配置；往 index 中写入的一条数据就是 document，一条 document 就相当于 mysql 中某个表的一行，每个 document 中有多个 field，每个 field 代表了这个 document 中一个字段的值。

创建在 es 中的 index 会被拆分为多个 shard，每个 shard 中会存放 index 中的一部分数据。接着 shard 中的数据还会进行多个备份，也就是说每个 shard 中都有一个 primary shard，负责写入数据，但是还有几个 replica shard（一般 primary 和 replica 不在同一台设备上）。primary shard 写入数据之后会将数据同步到其他几个 replica shard 上去。但是客户端进去数据的读取的时候是可以通过 primary shard 或者 replica shard 任何一个 shard 都可以的。也就是说写入只可以是 primary shard，而读取 primary 和 replica 都是可以的。

整个 es 集群会自动选举一个一个节点作为 master 节点，这个 master 节点其实就是干一些管理的工作，比如维护元数据以及负责切换 primary shard 和 replica shard 的身份等。假如 master 节点宕机了则会重新选举一个节点为 master 节点。

节点宕机之后其上面的 primary shard 的身份会有对应的 replica shard 来代替成为新的 primary shard。当宕机的机器修复好之后 mater 节点会将缺失的 replica shard 分配过去，同步后续修改的数据，让集群恢复正常。

以上就是 es 的整体分布式架构原理。

### 2.es写入和查询数据的原理是什么？

1）es写数据原理：

1、客户端随机选择一个 node 发送写数据请求，这个 node 就被作为 coordinating node（协调节点）

2、coordinating node 对 document 进行 hash 判断其所要保存的 shard ，然后执行路由操作将其转发到具有该 shard 的 primary shard 的 node 上。

3、具有 primary shard 的 node 上的 shard 对数据进行写入操作，并将数据同步到 replica shard 上去。

4、coordinating node 发现 primary 和 replica shard 都完成数据保存操作之后会返回对应的响应信息给客户端。

2）es读数据原理：

查询，GET一条数据，写入了一个 document，这个 document 会自动分配一个全局唯一的 id，doc id，同时也是根据 doc id 进行 hash 路由到对应的 primary shard 上。

然后就可以通过 doc id 来进行查询，node 会根据 doc id 进行 hash，判断出来当时把 doc id 分配到那个 shard 上就从那个 shard 上去查询。

1、客户端随机选择一个 node 发送读取数据请求，该 node 作为 coordinating node

2、coordinating node 对 document 进行 hash 选择保存数据的 shard，然后将消息转发到具有该 sahrd 的 node 上，此时会使用 round-robin 随机轮询算法在 primary 和 replica 中随机选择一个让读请求进行负载均衡。

3、执行该请求的 node 会返回对应的 document 给 coordinating node

4、coordinating node 返回 document 给客户端。 

3）es索引数据原理：

es最强大的就是做全文检索

1、客户端随机选择一个 node 发送索引数据请求，该 node 作为 coordinating node

2、coordinating node 将搜索请求转发到所有的 shard 对应的 primary sahrd 或者 replica shard ，接下来分为两步来执行

3、query phase：每个shard将自己的搜索结果（doc id）返回给协调节点，由协调节点进行数据的合并、排序、分页等操作，产生最终的结果

4、fetch phase：接着由协调节点根据 doc is 去各个节点上拉取实际的 document 数据，最后返回给客户端。

### 3.es在数据量很大（几亿级别）的情况下如何提高查询效率？

实际上来说，es性能优化是没有什么银蛋的。也就是不能通过调节参数来应对所有的性能慢的场景。需要一点点来分析：

在海量数据的场景下，如何提升 es 的性能。（主要就是 es 的底层实现原理的考查，针对底层实现原理来优化其中最重要的 filesystem cache）

**1）性能优化的杀手锏 - filesystem cache**

往es写入的数据实际都写入到磁盘文件中，磁盘文件里的数据操作系统会自动将其缓存到 os cache 里面去。也就是真正的数据保存在磁盘文件中。

然后当有客户端来读取索引的时候，会首先通过 es 的 shard 来向操作系统来读取数据，这时其实会首先先访问操作系统的 filesystem cache，当 cache 中没有存在这些数据的时候才会去从磁盘文件中去读，此时就非常浪费时间了。由此可见，es 的搜索引擎严重依赖与底层的 filesystem cache，因此如果给 filesystem cache 更多的内存，尽量让内存可以容纳所有的 index segment file 索引数据文件，那么你搜索的时候就基本都是走内存的，性能会非常高。

因此想让 es 的性能要好，最佳情况下要保证机器的内存可以至少容纳总数据量的一半，也就是说仅仅在 es 上存储少量的数据。同时也可以尽可能的让 es 存储比较重要的数据，将一些比较重要的数据用来检索的数据存储在es，提高效率。

一般是**使用 es + hbase 架构**。hbase的特点是适用于海量数据的在线存储，存储在 hbase 中的数据不需要做复杂的搜索，只需要进行简单的根据 id 或者范围来进行查询即可。这样使用 es 来查询到 doc id，然后使用 doc id 来去 hbase 中去查询每个 doc id 对应的数据。或者放在 mysql 里也是可以的。

一般来说**写入的数据应该小于 filesystem cache 的内存容量，或者略微大于**。

**2）缓存预热**

加入在 es 中保存的数据还是超过了 filesystem cache 的大小。这时候还可以通过数据预热的机制来将一些比较热门的数据进行数据预测处理。也就是自己开发一个数据预热系统，然后定期的将一些比较热门的数据进行读取，这样就会将这些比较热门的数据刷入到 filesystem cache 中，也可以做到提高数据检索的效果。

**3）冷热分离**

可以将索引数据中比较常用的数据和使用较少的数据分开来存储到不同的 index 中。然后保持热数据也就经常查询的数据全都存放在 filesystem cache 中，而那么冷数据放在磁盘中也是无所谓的。

**4）document模型设计**

为了保证检索的速度，可以将需要检索的数据格式在写入的时候就直接创建好，然后直接进行检索。不要将类似于表关联之类的操作放在检索的过程中。

**5）分页检索**

es 的分页检索的实现原理是将需要分页的数据的前面所有的数据从每一个 shard 中取出来，然后在协调节点中进行相关分页的实现。也就是说分页的页数越深的话需要从其他 shard 上读取的数据就越多，也就导致效率越低。

可以的话尽量避免在系统中提供比较深的分页的功能。或者直接提示用户较深的分页是需要更多的时间来完成的。

另外的话可以像一个 APP 一样（微博）使用 scroll api 来将所有的数据建立快照保存，然后一点一点的滑动，这样就不会出现跳页的情况。性能也会有所改善。

### 4.es生产集群的部署架构是什么？

虽然自己没有部署过，但是可以云淡风轻的大体上说一些

（但是校招的话可以简单的介绍下，并没有在生产环境中部署过，这道题可能也就这么过去了）

es生产集群我们部署了3台机器，每台机器是6核64G的，集群总内存是 192 G

es集群的日增量数据大概是500万条，每天日增量数据大概是 200MB，每月数据大概是1.5亿，6G。目前系统已经运行了几个月了，现在es集群里的数据大概是50G左右。

目前线上总共有3个索引，每个索引的数据量大概是20G，在这个数据量之内，我们每个索引分配的是8个shard。比默认的5个shard多个3