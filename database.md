# 数据库索引设计及其优化

## 索引设计

- 三星索引(JOIN, where/order by, select xxx)

- 索引的本质是用写入时间换取查询时间

- 索引一般有哈希表和B树家族，还有bitmap

    - B树把KV都放在节点上
    - B+树把K放在节点上，V放在叶子节点上

- 选择索引应当选择区分度大的列

    - https://tech.meituan.com/mysql-index.html
    - 最左前缀匹配原则
    - =和in可以乱序
    - 尽量选择区分度高的列作为索引
    - 索引列不能参加计算
    - 尽量的扩展索引，不要建新的索引

## 相关概念

- 连接

    - [数据库连接](https://zh.wikipedia.org/zh-hans/%E8%BF%9E%E6%8E%A5_(SQL))
    - [JOIN和INNER JOIN的区别](https://stackoverflow.com/questions/565620/difference-between-join-and-inner-join)

    - INNER：内连接。JOIN默认就是INNER JOIN。这两个没有任何区别，除了读法不一样。

        - 相等连接：`SELECT * FROM A JOIN B ON A.some_id = B.some_id` 或 `SELECT * FROM A JOIN B USING (some_id)`
        - 自然连接： 自然连接比相等连接的进一步特例化。两表做自然连接时，两表中的所有名称相同的列都将被比较，这是隐式的。
        自然连接得到的结果表中，两表中名称相同的列只出现一次。`SELECT * FROM   employee NATURAL JOIN department`
        - CROSS： 交叉连接，笛卡尔积。 `SELECT * FROM employee, drpartment`。从A表for循环B表。

    - FULL OUTER： 全外连接

        - LEFT OUTER： 左外连接，LEFT OUTER JOIN，简写成LEFT JOIN
        - RIGHT OUTER： 右外连接，RIGHT OUTER JOIN，简写成RIGHT JOIN
        - 全连接：全连接是左右外连接的并集. 连接表包含被连接的表的所有记录, 如果缺少匹配的记录, 即以 NULL 填充。

    - `self-join`：在特定的情况下，一张表可以和自身进行连接，称为自连接

- 连接算法

    - 循环嵌套（LOOP JOIN）：类似于C里的for嵌套
    - 合并连接（MERGE JOIN）：类似于两个有序数组的合并。两个输入表都在合并列上排序，然后依序对两张表逐行做连接或舍弃。
    如果预先建好了索引，合并连接的时间复杂度是线性的。
    - 哈希连接（HASH JOIN）：适用于查询的中间结果，通常是无索引的临时表，以及中间结果的行数很大时。

- 事务隔离级别

    - 未提交读
    - 已提交读
    - 可重复读
    - 串行读

- 事务的特性

    - Atomicity：原子性
    - Consistency：一致性
    - Isolation：隔离性
    - Durability：持久性

## 数据库优化

    - 加缓存
    - 检查硬件是否达到应有的标准
    - EXPLAIN并且检查索引是否设计正确
    - 加硬件
    - 读写分离，一主多从
    - 垂直分表（把不相关的字段拆走）
    - 水平分表（例如定期归档）
    - 垂直分库（如微服务化）
    - 水平分库（sharding）

- 覆盖索引

https://stackoverflow.com/questions/62137/what-is-a-covered-index

`select * from table where conditions` 假设conditions有用到索引，那么查询是先使用conditions，然后下降到B+树的叶子节点
拿数据，这需要扫描搜索出来的行。而如果是 `select a, b from table where conditions`，conditions, a, b都有索引的话，a和
b就可以直接在B+树的节点中取值而不需要去叶子节点拿到对应的行来取值了。这就叫索引覆盖，或者覆盖索引。EXPLAIN的时候如果显示
`Using index` 就说明是索引覆盖。

- primary key 和 unique key的区别

https://stackoverflow.com/questions/9565996/difference-between-primary-key-and-unique-key

    - 主键只能有一个
    - 主键不能为NULL
    - 主键是unique key但是反之不成立
    - unique可以有多个
    - unique可以有NULL，而且可以有多个
    - It can be a candidate key?(https://en.wikipedia.org/wiki/Candidate_key)

- 索引设计

    - 索引的本质是用写入时间获取读取时间
    - 尽可能的复用索引，而不建重复的索引，如：有UNIQUE就不需要另建索引
    - 尽可能选取区分度大的索引
    - 使用覆盖索引
    - 设计三星索引
