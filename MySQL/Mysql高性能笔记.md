### 1.Mysql的架构历史

- 架构逻辑：第一层负责连接处理、授权操作、安全认证，第二层查询解析、分析、优化、缓存，第三层存储引擎负责Mysql中数据存储和提取

- **并发控制**
    - 表锁和行锁，也叫共享锁和排它锁
    - 事务：原子性、一致性、隔离性、持久性（ACID）
    - 隔离级别：未提交读、提交读、可重复读、课串行化
      隔离级别 | 脏读 | 不可重复读 | 幻读
      ---|---|---|---
      未提交读 | Yes | Yes | Yes
      提交读 | No | Yes | Yes
      可重复读 | No | No | Yes
      串行化 | No | No | No

    - 死锁：指两个和多个事务在同一资源上相互占用，并请求锁定对方占用的资源，导致恶性循环现象

- 多版本控制(MVCC)：可以认为MVCC是行集锁的一种，InnoDB的MVCC是通过每行记录后面保存两个隐藏列来实现的，一个是保存行的创建时间，一个是保存行的过期时间。只有在读提交和可重复读两个隔离级别下有用

- InnoDB引擎：InnoDB采用MVCC来支持高并发，并实现四个标准的隔离级别，其默认级别是可重复读，并通过间隙锁策略防止幻读的出现

- 选择合适的引擎：需考虑因素(事务、备份、崩溃恢复、特有的特性)，除非需要用到某些InnoDB不具备的特性，并且没有其他办法替代，否则都应该优先选择InnoDB引擎。建议不要混合使用多种存储引擎


### 2.Mysql基准测试

- 基准测试策略：一是针对整体系统的测试，另外是单独测试Mysql，这两种策略也被称为集成式以及单组件式基准测试

- 测试指标：吞吐量、响应时间或延迟、并发性、可扩展性

- 基准测试工具：集成式测试工具：ab、http_load、JMeter，单组件式测试工具：sysbench

### 3.服务器性能剖析

- 定义性能最有效的方法是响应时间

- Mysql通过设置```long_query_time```值来捕获慢查询

### 4.Schema与数据类型优化

- 选择数据类型：
    - 整数类型：
        - TINYINT、SMALLINT、MEDIUMINT、INT、BIGINT分别对应8、16、24、32、64位存储空间，存储的值范围为-2<sup>(N-1)</sup>-2<sup>(N-1)</sup>-1，其中N是存储空间的位数
        - 整数类型有可选UNSIGNED属性，表示不允许负值，使整形正数的上线提高一倍
        - Mysql可以为整型指定宽度，如int(11)，对大多数应用没有意思，只是规定了交互工具用来显示字符个数
    - 实数类型：
        - FLOAT和DOUBLE类型支持使用标准的浮点运算进行近似计算
        - DECIMAL类型可以指定小数点前所允许的最大位数
    - 字符串类型：
        - VARCHAR类型用于存储可变长字符串，是最常见的字符串数据类型
        - CHAR类型是定长的
        - BLOB和TEXT类型为存储很大的数据而设计的数据存储类型，分别采用二进制存储和字符串存储
        - ENUM类型存储预定义集合的字符串类型
    - 日期和时间类型：
        - DATETIME和TIMESTAMP类型区别在于前者不考虑时区，后者提供Mysql内置函数处理格式
        - 尽量使用TIMESTAMP类型存储效率更高
        - 用整形存储Unix时间戳，这不会带来任何收益，不推荐这样做
    - 位运算类型：
        - BIT和SET：通常用来存储true/false值
    - 特殊类型数据：
        - IPv4地址通常使用VARCHAR(15)来存储ip地址

- Mysql schema设计的陷阱：太多的列、太多的关联、全能变相的枚举、非此发明的NULL(替换bull)

- 范式和反范式：
    - 范式和反范式：范式更新操作比反范式操作快，反范式不需要关联表避免全表扫描
    - 混用范式化：在实际开发过程中，通常使用范式和反范式相结合混合开发。

- 总结：
    - 更小的通常更好、简单就好、尽量避免NULL
    - 尽量使用整型定义标识列
    - 尽量使用相同数据类型存储相似的值
    - 避免使用ENUM、SET、BIT

### 5.创建高性能的索引

- 索引是存储引擎用于快速查找记录的一种数据结构

- 索引类型：
    - B-Tree：是最常用的数据结构来存储数据，通常是意味着所有的值按顺序存储，适用场景：全值匹配、匹配最左前缀、匹配列前缀、匹配范围值、匹配范围值、覆盖索引
    - 哈希索引：基于哈希表实现，只有精确匹配索引所有列才有效
    - 空间数据索引
    - 全文索引

- 索引的优点：
    - 索引大大减少了服务器需要扫描的数据量
    - 索引可以帮助服务器避免排序和临时表
    - 索引可以将随机I/O变成顺序I/O

- 高性能索引策略：
    - 独立的列：索引列不能是表达式的一部分，也不能是函数的参数
    - 前缀索引：选择该列的部分字符作为索引，减少索引的字符可以节约索引空间，从而提高索引效率，但这样也会降低索引的选择性
    - 索引的选择性：不重复的索引值和数据表的记录总数的比值
    - 多列索引：当服务器对多个索引做相交或联合操作，就应该把多个单列索引进行索引合并创建所有相关列的多列索引
    - 选择合适的索引顺序：一般将选择性最高的列放在索引的最前面
    - 聚簇索引：不是一种单独的索引类型，而是一种数据存储方式，表示数据行和相邻的键值紧凑地存储在一起，一个表只能有一个聚簇索引，一般通过设置主键列为聚簇索引，在MySQL中，只用Innodb引擎才采用聚簇索引，其他的存储引擎像MyISAM采用非聚簇索引
    - 覆盖索引：只需要在一棵索引树上就能获取SQL所需的所有列数据，无需回表，explain的输出结果Extra字段Using index时，能够触发索引覆盖，常见的方法是：将被查询的字段，建立到联合索引里去
    - 使用索引来做扫描：mysql有两种方式可以生成有序的结果，通过排序操作或者按照索引顺序扫描。如果explain出来的type列的值为index,则说明MySQL使用了索引来扫描做排序，orderby语句需要满足索引最左前缀的要求，否则无法使用索引来排序，如果where字句或者join字句中对这些列指定了常量则不需要满足要求

- 维护索引和表：
    - 修复表：REPAIR TABLE、myisamchk、mysqlcheck
    - 更新索引统计信息：ANALYZE TABLE来重新生成统计信息
    - 减少索引和数据的碎片：OPTIMIZE TABLE或导出再导入修复

- 总结：
    - 索引设计原则：
        - 对于经常查询的字段，建议创建索引
        - 避免过多的索引
        - 在选择性高的字段建立索引
        - 当唯一性是某字段本身的特征时，指定唯一索引能提高查询速度
        - 在频繁进行跑排列分组（即进行 group by 或 order by操作）的列上建立索引，如果待排序有多个，可以在这些列上建立组合索引
        - 按顺序访问范围数据会很快
        - 索引覆盖查询很快
        - 尽量使用短索引
    - 判断索引的合理性：
        - 找出耗时长或压力大的查询
        - 分析schema，SQL和索引结构
        - 是否查询了太多的行
        - 是否做了很多额外的排序或使用了临时表
        - 是否使用随机I/O访问数据
        - 是否有太多回表操作
    - 索引未命中原因：
        - 范围问题：查询范围不明确，达不到优化效果
        - 保持索引列"干净"：不能有列计算或者函数
        - 最左前缀原则：字段顺序需一致
        - 类型不一致：连表时注意条件类型需一致，俗称隐式转化


### 6.查询性能优化

- 查询开销的三个指标：
    - 响应时间：服务时间和排队时间，一般常见和重要的等待是IO和锁等待
    - 扫描的行数和返回的行数
    - 扫描的行数和访问类型

- 重构查询方式：
    - 复杂查询转化成多个简单查询
    - 切分查询：把大查询或者大执行语句切分执行，例如chunk
    - 分解关联查询

- 查询执行的基础：
    - 1.客户端发送一条数据
    - 2.服务端检查缓存，有则返回，无则继续
    - 3.服务端对MySQL进行解析、预处理
    - 4.优化器生成执行计划，调用存储引擎Api执行查询
    - 5.返回结构给客户端

- 查询状态：
    - SHOW FULL PROCESSLIST命令查看查询的执行状态
    - SHOW STATUS命令查询状态值

- 常用优化技巧：
    - 添加索引：主键索引，普通索引，唯一索引
    - 优化数据库结构：字段分解成多个表，增加中间表
    - 分解关联查询：将一个大的查询分解为多个小查询
    - 优化LIMIT分页：先查询出主键id值、关延迟联

- Explain分析
    - id：SELECT的查询序列号，SQL执行的顺序的标识，SQL从大到小的执行
    - select_type：查询中子句的类型
    - table：数据库中表名称或者简称
    - type：访问类型，常用的类型有：ALL、index、range、 ref、eq_ref、const、system、NULL（从左到右，性能从差到好）
    - possible_keys：查询涉及到的字段上若存在索引
    - key：显示MySQL实际决定使用的键（索引），必然包含在possible_keys中
    - key_len：表示索引中使用的字节数
    - ref：列与索引的比较
    - rows：找到所需的记录所需要读取的行数
    - Extra：
        - Using where：表明使用where过滤
        - Using filesort：对数据适用一个外部的索引排序，文件排序
        - Using temporary：查询结果排序时使用临时表，常见于排序order by和分组查询group by
        - Using index：覆盖索引

### 7.MySQL高级特性

 
