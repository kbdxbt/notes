**ElasticSearch**
- Elasticsearch 是一个实时的分布式存储、搜索、分析的引擎，特点：高容量、高吞吐、高可用、支持多维度数据分析和处理、api简单易用

**ElasticSearch官方api文档**
https://www.elastic.co/guide/en/elasticsearch/reference

**ElasticSearch架构图**
![image](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7bfa3add86dd47099079cc36bc887c7b~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

**ElasticSearch层级**
![image](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7e451e1f0f5c49669c1348a4c4bd314d~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp?)

**ElasticSearch应用场景**
- 全文检索
- 分布式数据库
- 日志分析
- 运维监控
- BI系统

**ElasticSearch基础概念**
- Node（节点）：单个装有ES的服务器。
- Cluster（集群）：一个ES集群就是由一个或多个Node组成（每个Node都是对等关系，去中心化）。
- Document（文档）：一个文档就是一个可被索引的基础信息单元
- Index（索引）：索引就是一个拥有相似特征的文档的集合
- Type（类型）：一个索引中，你可以定义一种或多种类型（7.x版本及之后已经剔除）
- Filed（字段）：Filed是ES的最小单位，相当于数据的某一列
- Shards（分片）：ES将索引分成多份，每一份就是一个Shard
- Replicas（复制）：Replicas是索引一份或多份拷贝

| Elasticsearch | RDBMS |
| --- | --- |
| Index/Type（索引/类型） | Table（数据库） |
| Type（类型） | Table（表） |
| Document（文档） | Row（行） |
| Field（列） | Column（字段） |
| Mapping（映射） | Schema（结构） |
| DSL | SQL |

**ElasticSearch倒排索引**
- 通过分词策略，形成了词和文章的映射关系表，这种词典+映射表即为倒排索引， 基于FST数据结构，空间占用小，查询速度快

**Elasticsearch调优手段**
- 设计调优
    - 使用别名进行索引管理
    - 采取冷热分离机制，热数据存储在SSD
    - 设置合理副本数
    - 定时进行合并优化
- 写入调优
    - 使用bulk批量写入
    - 设置副本数为0
    - 设置刷新时间为30s
- 查询优化
    - 禁用wildcard
    - 尽可能使用keyword类型

**Elasticsearch选举master**
- 前置前提：候选主节点，最小主节点数（防止脑裂）
- 实现：确认候选主节点数达标，判断master资格，优先使用id小的值为主节点


**ElasticSearch节点**
master节点的职责主要包括集群、节点和索引的管理，不负责文档级别的管理；data节点可以关闭http功能