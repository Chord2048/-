# CMU15445

### 面试问到的问题

##### 了解可重入锁吗？

可重入锁是指线程可以多次获取同一个锁，而不会发生**阻塞**或者**死锁**。

1. **线程再次获取锁**：
   - 每个锁关联一个**线程持有者**和**计数器**。
   - 当线程第一次获取锁时，计数器置为1。
   - 如果同一线程再次请求这个锁，计数器递增。
   - 这样，同一个线程可以反复获取它占有的锁，实现可重入性。
2. **锁的最终释放**：
   - 当线程释放锁时，计数器递减。
   - 如果**计数器为0**，表示锁已成功释放，其他线程可以获取该锁。

C++ recursive_mutex

![img](https://pic4.zhimg.com/80/v2-12bde2c60081ddf8b60bd765cb55ebe7_1440w.webp)

##### 隔离级别怎么实现？

* 读未提交：不用加 S IS 锁
* 读提交：shrinking 阶段只能加 S IS 锁
* 可重复读：shrinking 阶段不能加任何锁



##### 流程





## Project 1

为什么用 LRU-K

LRU 的问题：缓存污染和预读失败

* 预读失败：加载时将相邻页加载进来。这些页有可能不用但是会占据 LRU 的头部，并且还可以替换掉当前存在的页面。LRU-K 使预读页更容易被换出。

* 缓存污染：扫描大量数据可能把热数据淘汰掉。造成热数据的缓存命中率下降。访问达到 K 次才能进入热区，LRU-K 使得热数据不容易被换出。

LRU-K的主要目的是为了解决LRU算法“**缓存污染**”的问题，其核心思想是将“**最近使用过1次**”的判断标准扩展为“**最近使用过K次**”。也就是说**没有到达K次访问的数据并不会被缓存**

相比于 LRU-1 缓存数据不容易被替换，偶发性（不频繁）的数据不易被缓存。在保证了数据纯净度的同时还提高了热点数据的命中率。

此外，LRU-K 中的 K 可以动态调整。适应不同的负载。

##### MYSQL 的做法

young 和 old 大概时 2 : 1

* 解决预读失败：**预读**的页加入 old 区域，被访问时加入 young 区域。
* 解决缓存污染：提高进入 young 区的门槛，增加停留在 old 区的**时间判断**。（1s）



![img](https://cdn.xiaolincoding.com/gh/xiaolincoder/ImageHost4@main/mysql/innodb/young%2Bold.png)



#### LRU、LFU、FIFO 优缺点

在实际应用中，LRU、LFU和FIFO各有优缺点。

本质：LRU 是基于时间的、LFU 是基于频率的

* 对于LRU来说，它的优点是**实现简单，时间复杂度较低**；缺点是对数据的**热度敏感度不高，可能会淘汰热数据**。
  * 偶然性的全量数据遍历会造成缓存污染。
* 对于LFU来说，它的优点是能够**根据数据的使用次数进行淘汰**，更符合实际情况；缺点是**实现较复杂，时间复杂度较高**。
  * 需要维护频率信息
  * 对突发性的**稀疏数据**无力
  * 大量访问的数据**未来不一定会用上**，占用坑位
* 对于FIFO来说，它的优点是实现简单，时间复杂度较低；缺点是**对数据的热度不敏感**，可能会导致热数据被淘汰。

LRU 对频次不敏感，可能换出热数据。

LFU 频次高的数据不一定未来会用上。





#### Redis 内存淘汰策略有哪些？

大概分为三类

- 报错
- 根据有**过期时间**淘汰
  - volatile-random,**随机**淘汰有过期时间的key
  - volatile-ttl,淘汰**最早过期**的
  - volatile-lru,淘汰**最近最少使用的**
  - volatile-lfu,淘汰**使用频率最低**的
- 全体范围内淘汰
  - allkeys-random，随机淘汰
  - allkeys-lru,淘汰最近最少使用的
  - allkeys-lfu,淘汰使用频率最低的

**Redis 是如何实现 LRU 算法的？**

redisObject保存着一个**lru字段保存着最后一次访问的时间戳**，不使用传统的链表，而是记录一个最后一次访问的时间，淘汰最后访问时间最早的。

优点：节省内存

缺点：会导致缓存污染问题，只访问一次的数据会将之前的热点数据淘汰掉

**Redis 是如何实现 LFU 算法的？**

复用了reidsObject的lru字段，将**lru字段分为两部分**，一部分记录**最后访问的时间戳**，另外一部分**记录热度**，热度会随着时间逐步降低，访问次数越多，热度也会越高，但**不是线性增长，热度越高热度越难增加**。





脏页什么时候换出？

mysql 脏页换出：

* redo log 满了
* 脏页被换出
* 空闲时，后台线程完成
  * 一定时间？
  * 脏页达到一定比例
* 正常关闭





## Project 3

![img](https://pic3.zhimg.com/80/v2-099730a10fe1b64f277f88f5f39f5af2_720w.webp)

##### **Parser**

一条 sql 语句，首先经过 Parser 生成一棵抽象语法树 AST。

**Binder**

在得到 AST 后，还需要将这些词语**绑定到数据库实体**上，这就是 Binder 的工作。例如有这样一条 sql：

**Planner**

得到 Bustub AST 后，Planner 遍历这棵树，生成初步的查询计划。查询计划也是一棵树的形式。例如这条 sql：

**Optimizer**

1. Rule-based. Optimizer 遍历初步查询计划，根据已经定义好的一系列规则，对 **PlanNode** 进行一系列的修改、聚合等操作。例如我们在 Task 3 中将要实现的，将 Limit + Sort 合并为 TopN。这种 Optimizer 不需要知道数据的具体内容，仅是根据预先定义好的规则修改 Plan Node。
2. Cost-based. 这种 Optimizer 首先需要读取数据，利用统计学模型来预测不同形式但结果等价的查询计划的 cost。最终选出 cost 最小的查询计划作为最终的查询计划。

另外值得一提的是，一般来说，Planner 生成的是 Logical Plan Node，代表抽象的 Plan。Optimizer 则生成 Physical Plan Node，代表具体执行的 Plan。一个比较典型的例子是 Join。在 Planner 生成的查询计划中，Join 就是 Join。在 Optimizer 生成的查询计划中，Join 会被优化成具体的 HashJoin 或 NestedIndexJoin 等等。在 Bustub 中，并不区分 Logical Plan Node 和 Physical Plan Node。Planner 会直接生成 Physical Plan Node。

**Executor**

遍历查询计划树，将树上的 **PlanNode** 替换成对应的 **Executor**。算子的执行模型也大致分为三种：



火山模型**占用内存小**，实现简单。但是**虚函数开销大**。慢一点。



Table 设计

![img](https://pic3.zhimg.com/80/v2-9bc6214441f8ca37004ff1389114a692_720w.webp)

##### Seq Scan

直接用 iterator



##### **Insert & Delete**

Insert 和 Delete 这两个算子实现起来基本一样，也比较特殊，是唯二的写算子。数据库最主要的操作就是增查删改。Bustub sql 层暂时不支持 UPDATE。Insert 和 Delete 一定是**查询计划的根节点**，且仅需**返回一个代表修改行数的 tuple**。

Insert 和 Delete 时，记得要更新与 table 相关的所有 index。index 与 table 类似，同样由 Catalog 管理。需要注意的是，由于可以对不同的字段建立 index，一个 table 可能对应多个 index，所有的 index 都需要更新。

Insert 时，直接将 tuple 追加至 table 尾部。Delete 时，并不是直接删除，而是将 tuple 标记为删除状态，也就是逻辑删除。（在事务提交后，再进行物理删除，Project 3 中无需实现）

Insert & Delete 的 `Next()` 只会返回一个包含一个 integer value 的 tuple，表示 table 中有多少行受到了影响。



##### **IndexScan**

使用我们在 Project 2 中实现的 **B+Tree Index Iterator**，遍历 B+ 树叶子节点。由于我们实现的是**非聚簇索引**，在叶子节点只能获取到 RID，需要拿着 RID 去 table 查询对应的 tuple。

在完成 Task 1 的四个算子后，可以用已提供的 sqllogictest 工具和已提供的一些 sql 来检验自己的算子是否实现正确。



##### **Aggregation**

会打破 iterator model， 需要在 init 里计算出结果。

```c++
/** The aggregation plan node */
const AggregationPlanNode *plan_;
/** The child executor that produces tuples over which the aggregation is computed */
std::unique_ptr<AbstractExecutor> child_;
/** Simple aggregation hash table */
SimpleAggregationHashTable aht_;
/** Simple aggregation hash table iterator */
SimpleAggregationHashTable::Iterator aht_iterator_;
```

```c++
void AggregationExecutor::Init() {
  child_executor_->Init();
  aht_.Clear();
  Tuple child_tuple;
  RID rid;
  // fmt::print("Agg group_by size:{}\n", plan_->GetGroupBys().size());
  // fmt::print("Agg OutputSchema:{}\n", plan_->output_schema_);
  while (child_executor_->Next(&child_tuple, &rid)) {
    auto agg_key = MakeAggregateKey(&child_tuple);
    auto agg_value = MakeAggregateValue(&child_tuple);
    aht_.InsertCombine(agg_key, agg_value);
  }
  if (aht_.Empty() && plan_->GetGroupBys().empty()) {
    aht_.MakeEmpty({});
  }
  aht_iterator_ = aht_.Begin();
}
auto AggregationExecutor::Next(Tuple *tuple, RID *rid) -> bool {
  if (aht_iterator_ != aht_.End()) {
    std::vector<Value> values;
    if (!plan_->GetGroupBys().empty()) {
      values = aht_iterator_.Key().group_bys_;
    }
    values.insert(values.end(), aht_iterator_.Val().aggregates_.begin(), aht_iterator_.Val().aggregates_.end());
    *tuple = Tuple{std::move(values), &plan_->OutputSchema()};
    ++aht_iterator_;
    return true;
  }
  return false;
}
```

### NestedLoopJoin

```c++
// 暂存 left tuple 和右表
while (left_child->Next(&left_tuple)){
    for (auto right_tuple : right_tuples){
        if (left_tuple matches right_tuple){
            *tuple = ...;   // assemble left & right together
            return true;
        }
    }
}
return false;
```







#### Hash Join

in-memory的hashjoin 比较简单。

* init 阶段遍历左表建立 hashmap
* next 函数中查找 hashmap 检查是否有匹配的tuple。

hashmap需要我们自己重载重载operator==()并实现std::hash，具体可以参照agg_plan.h中的AggregateKey。使用joinexpr的evalue方法获得key。









### Sort

Sort 也是 **pipeline breaker**。在 `Init()` 中读取所有下层算子的 tuple，并按 ORDER BY 的字段升序或降序排序。Sort 算子说起来比较简单，实现也比较简单，主要需要自定义 `std::sort()`。



### Limit

和 SeqScan 基本一模一样，只不过在**内部维护一个 count**，记录已经 emit 了多少 tuple。当下层算子空了或 count 达到规定上限后，不再返回新的 tuple。





### TopN

直接用 `std::priority_queue` 加自定义比较函数。在 init 里遍历下层的 tuple。







### 优化



#### TOPN优化

**后序遍历**

能优化为一个 TopN 算子的形式是，**上层节点为 Limit，下层节点为 Sort**，不能反过来。同样，我们对 plan tree 进行**后续遍历**，在**遇到 Limit 时，判断其下层节点是否为 Sort，若为 Sort，则将这两个节点替换为一个 TopN**。还是比较好实现的，只是代码看起来可能有点复杂。

#### JoinOrder 优化

优化JoinOrder，用t3小表驱动t2大表。用 `EstimatedCardinality()` 函数估计表的大小，交换左右计划并重写predicate。


>  建议在MySQL关联查询时，将小表作为驱动表，而将大表作为被驱动表。这是因为**小表通常拥有较少的记录**，而大表可能包含大量的数据。
>
> 当小表作为驱动表时，MySQL可以先从小表中获取数据，并使用这些数据来查询大表。这样做的好处是可以**减少查询的数据量**，提高查询的效率。如果将大表作为驱动表，MySQL需要先扫描大表的所有记录，然后再根据关联条件查询小表，这样会增加查询的时间和资源消耗。
>
> 建议将小表放在 JOIN 语句中的前面，作为驱动表，优先进行数据筛选和过滤，这有利于提升数据库查询效率并且让查询更快地完成。



#### 谓词下推

先序遍历

PredicatePushDown。即谓词下推。采用**先序遍历**的方式，自顶向下优化NLJ plan。
遍历NLJ的predicate，将 Filter predicate中的表达式分为三类。



![img](https://pic4.zhimg.com/80/v2-4e5742b8da03b2fb22d0c7482824c713_720w.webp)

先序遍历，自顶向下下推 where 的谓词。

需要注意的时，我们下推的不是整个 Filter 节点，实际上是节点中的 predicate。我的做法是遍历表达式树，提取 predicate 中的所有 comparison，判断表达式的两边**是否一个是 column value，一个是 const value**，只有这样的 predicate 可以被下推

**两边都为 column value** 且分别代表左右两个下层算子的某一列的 Filter 可以被结合到 Join 节点中作为 Join 条件。这一步的规则已经被实现好了，因此这种 Filter 我们让其停留在原地即可。

总结：

* 一个常数一个列值，下推到这有这个列值的。
* 两个列值，下推合并到 join 的条件中。

![img](https://pic1.zhimg.com/80/v2-0ef5d36670cbd9abb664138ad68e6af0_720w.webp)





#### 聚合函数、Group By 是如何实现的





#### Join 如何实现的

Nested Loop Join 二维循环遍历所有的结果

index join, hash join

追问：这种方法有什么优化？答了索引优化、哈希表优化

哈希表优化的方式，如果建立哈希表特别的大，右边的特别小怎么办？

- 答了系统会做优化，选取左边或者右边比较小的那个表

追问：这个选择左边或者右边一般是在数据库哪个模块做的？（答：MySQL 当中是优化器）

大概知道这个优化器的实现原理吗？

- 扯了 MySQL 通过 Explain 语句，根据数据的行数选择决定用哪个表做哈希

追问：你知道 Explain 语句是怎么实现的吗？

介绍一下项目 Buffer Pool 的设计

脏页是怎么管理的？（答了 LRU 链表替换的时候，如果是脏页会刷新到磁盘，然后引导的说了一下这可能会导致数据丢失的问题）

追问：有什么办法解决这个脏页丢失的问题？

- 答：扯了下 MySQL 当中 redo 日志

有没有考虑过 LRU 算法的缺陷？（预读失效、缓存污染）

并发的 B+ 树如何实现的？

提到了火山模型，有了解到其他的执行模式吗？（答了物化模型，然后说这种适用于列数据库）

从你的角度来看，行存和列存主要的应用场景在哪？

数据库项目当中是否有事务？（答：有简单的 MVCC，但是没有具体了解过）

单机数据库，还是分布式数据库？（单机）

从这个单机数据库为基础，考虑拓展为分布式数据库，但是对于用户来说并没有感知，用户可能只是通过一个 IP 和地址进行访问，然后元数据的设计，数据的分布，算子的执行考虑如何设计？

- 先扯了一下数据会考虑分片，我说了通过分布式的 ID 进行访问

追问：你说的这种分片应该类似于哈希分片，还有什么分片方式？

面试官引导说顺序扫描会有什么问题吗？然后想到按照范围进行一个分片

元数据的话有什么样的优化点？（答：单独用一个数据库来保存元数据）

假设保存元数据的节点挂了，这个时候要怎么考虑呢？（答：用主从复制的结构，主节点挂了，选个从节点）

追问：在 MySQL 当中如果是主从复制的话，需要每次复制很多个从节点，你有了解业界用什么分布式的系统解决这个问题吗？（大概说了下 OceanBase 的三地五中心）

MySQL 主从复制结构中，主节点写入一条数据，从节点需要保证比较强的一致性，是如何处理的呢？（这里不太会，扯了下通过 bin log 日志进行同步）

这里面试官又问了，MySQL 通过 bin log 来同步的话，对于用户来说是希望写入数据之后，立刻能读取到数据，如何保证一致性？（这里应该想问 Raft 协议相关的了，不会）

看我上个问题不太会，面试官说这个问题可以抽象为分布式系统的一致性，这个有了解吗？（不了解）

select * from table; 的执行计划是什么样子的？（Project 投影算子 + TableScan 全表扫描算子，面试的时候 Project 一直想不起来叫啥了，服了）

如果是分布式数据库的话，数据分布在不同的地方，你觉得需要增加哪些算子来实现这个功能？（答：在 Project 和 TableScan 中间再加一层算子对所有的数据进行汇总）

如果加上 where 条件呢？分布式情况下如何考虑？

Group By 有一个 count，需要加上哪些算子呢？（答聚合函数算子，分布式情况下再进一步汇总）

简单聊一下 Linux 的开机启动过程（x86 系统的忘了）

中断处理模块是怎么实现的呢？

CPU 一般如何处理多个中断？

在你的系统中如何理解进程和线程的区别？（这里没按照八股答，答了进程和用户态线程）

用户态线程是自己主动退出的？这种实现有什么问题？（答了可能会一直不让出，占用 CPU）

用户态向内核态切换的时候，描述一下切换过程？

算法题：实现一个基于内存的文件系统，其实是一个变种的字典树问题

反问

问对实习地点是否有要求？说实习地点可以选北京、上海、深圳等，我说没特殊的要求







#### 一致性理论

线性调度

可串行化调度 A schedule that is equivalent to some serial execution of the transactions.

| item       | 描述                             | 举例                            |
| ---------- | -------------------------------- | ------------------------------- |
| 脏读       | 读到了其他事务写了但没提交的数据 | 事务1读事务2写的数据，事务2回滚 |
| 不可重复读 | 两次读不一致                     | 事务1两次读之间事务2写提交      |
| 幻读       | 读到之前没有的数据               |                                 |

**冲突可串行化**

写写冲突：更新丢失。One txn overwrites uncommitted data from another uncommitted txn.

A schedule is **conflict serializable** iff its **dependency graph** is acyclic

**视图可串行化**

允许盲写，允许更多调度。



## Project 4

https://zhuanlan.zhihu.com/p/600001968

https://www.cnblogs.com/looking-for-zihuatanejo/p/17529739.html#:~:text=%E4%BB%A5%E4%B8%8A%E5%8F%AA%E6%98%AF%E7%AC%BC%E7%BB%9F%E7%9A%84%E6%B5%81%E7%A8%8B,%E9%87%8A%E6%94%BE%EF%BC%8Ccommit%20%E9%87%8A%E6%94%BEX%20%E9%94%81



### 两阶段锁

2PL 用来决定事务能不能访问对象，不需要提前直到查询计划。

**Phase #1: Growing** → Each txn **requests** the locks that it needs from the DBMS’s lock manager. → The lock manager grants/denies lock requests. 

**Phase #2: Shrinking** → The txn is allowed to **only release/downgrad**e locks that it previously acquired. It cannot acquire new locks

基于两阶段锁理论，分为增长阶段和衰退阶段，增长阶段只能获取锁，一旦释放锁，进入衰退阶段，衰退阶段不能释放特定的锁。



原理：两阶段锁可以**防止依赖有向图有环**。



但是有**级联中止**问题。

由于 T1 中止了，T2 在之前读到 T1 写入的数据，就是所谓的 “脏读”。为了保证整个 schedule 是 serializable，DBMS 需要在 T1 中止后将曾经读取过 T1 写入数据的其它事务中止，而这些中止可能进而使得其它正在进行的事务级联地中止，这个过程就是所谓的级联中止。



#### 2PL 存在的问题：

- 可能出现**级联回滚** —— 解决方案：**严格**二阶段封锁协议 (**Strict 2PL, S-2PL**)
  - 提交了才能**释放互斥锁**
- 可能出现**脏读** —— 解决方案：**强**二阶段封锁协议 (**Strong Strict 2PL, SS-2PL，又称 Rigorous 2PL**)
  - 提交了才能**释放所有锁**
- 可能导致死锁 —— 解决方案：死锁检测和预防





严格两阶段锁 Rigorous 2PL

**每个事务在结束之前，其写过的数据不能被其它事务读取或者重写**

The txn is only allowed to release locks after it has ended (i.e., committed or aborted).



层次锁结构

![image-20240416134015855](C:\Users\19183\AppData\Roaming\Typora\typora-user-images\image-20240416134015855.png)

意向锁：**快速判断下层是不是有节点上锁了**

**IX 意向互斥**

**IS 意向共享**

**Shared+Intention-Exclusive  SIX锁**



SIX 锁 = S 锁 + IX 锁

* 对**目标表**加上 SIX 锁后，S 锁这一属性保证了**别的事务能知道我们要读取目标表的所有 record**，它们就**不会去并发地更新**任意的 record；
* IX 锁这一属性保证了别的事务能**知道我们要更新目标表的一部分** record，它们就**不会去并发地读取所有的 record**。



**为什么要有SIX锁？**读所有，写一部分。

事务**读取所有记录，并且改一部分**

如果没有SIX锁，只能对表加X（低并发），或者所有读记录加S，写记录加X（消耗高）

意向锁之间兼容。意向锁和其他锁要看有没有x。（IS 和 SIX 兼容）

![image-20240416134810444](C:\Users\19183\AppData\Roaming\Typora\typora-user-images\image-20240416134810444.png)

**加锁协议**

在数据库管理系统中，每个事务都会在数据库层次结构的最高级别上获取适当的锁：

- 要在节点上获取 S（共享锁）或 IS（意向-共享锁），事务必须至少持有其父节点上的 IS（意向-共享锁）。
- 要在节点上获取 X（独占锁）、IX（意向-独占锁）或 SIX（共享+意向-独占锁），事务必须至少持有其父节点上的 IX（意向-独占锁）。

同时，意向锁（IS 和 IX）的使用可以优化锁管理，**减少不必要的锁检查**，提高并发控制的效率。





#### 不同的隔离级别怎么做到的?

不同隔离级别是基于两阶段锁实现的，锁管理器管理共享、互斥和意向锁的授予和升降级。Bustub 是通过允许加锁的种类来控制隔离级别的。

(1):*REPEATABLE_READ* 

1. **shrinking 阶段不允许加任何锁。** 强2PL （strong strict），abort或者commit的时候统一解锁。

(2): READ_COMMITTED

1. **shrinking 阶段只允许加S IS锁** （也就是允许重新获得读锁）。不可重复读。
2. **提前释放 S 锁（GROWING）**，所以会两次读到不一样的数据

(3):*READ_UNCOMMITTED* 只允许X IX锁。读的时候不用**加读锁**，直接读。



#### 2PL怎么实现？

1. 如果一个transaction想要**读取** **数据项 x** ，那它必须获取 x 上的**shared lock**；如果一个transaction想要**写入** **数据项 x** ，那它必须获取 x 上的**exclusive lock**。

2. 如果一个transaction释放了它所持有的**任意一个锁**，那它就**再也不能获取任何锁**。

3. **Strict 2PL** 是 2PL 的变体，不仅要求封锁是两阶段的，还要求事务持有的**排他锁**必须在事务提交后才能一次性释放。

   **Strong Strict 2PL** 是 2PL 的变体，不仅要求封锁是两阶段的，还要求事务持有的**所有锁**必须在事务提交后才能一次性释放。



事务的初始状态是 growing，如果涉及到解锁操作，需要将事务的状态修改为 shrinking。

以上只是笼统的流程，project4 中，基于 2PL 实现了 3 种隔离级别：

- 读未提交：写时加X 锁，**读时不加锁**，**commit 释放 X 锁**
- 读提交：写时加X 锁，读时加 S 锁，**读完立即释放**，**commit 释放 X 锁**
- 可重复读：写时加 X 锁，读时加 S 锁，**commit 释放S、 X 锁**



#### 锁的种类？

X，S，SIX，IS，IX 五种

#### abort 怎么处理？





#### 兼容性检查

**不能简单查看先前记录中的granted字段**判断是否被授予，而是应该**重新计算先前的记录是否应该被granted，检查先前所有未授予的锁是不是和它们前面的兼容**

检查所有先前锁能不能被授予。不受notify顺序影响。

```c++
auto LockManager::LockRequestQueue::CheckCompatibility(LockMode lock_mode, ListType::iterator ite) -> bool {
  // all earlier requests should have been granted already
  // check compatible with the locks that are currently held
  // dp
  int mask = 0;
  assert(ite != request_queue_.end());
  ite++;
  // fmt::print(fmt::fg(fmt::color::orange_red), "check\n");
  for (auto it = request_queue_.begin(); it != ite; it++) {
    const auto &request = *it;
    const auto mode = request->lock_mode_;
    auto *txn = TransactionManager::GetTransaction(request->txn_id_);
    if (txn->GetState() == TransactionState::ABORTED) {
      continue;
    }

    auto confilct = [](int mask, std::vector<LockMode> lock_modes) -> bool {
      return std::any_of(lock_modes.begin(), lock_modes.end(),
                         [mask](const auto lock_mode) { return (mask & 1 << static_cast<int>(lock_mode)) != 0; });
    };

    // fmt::print(fmt::fg(fmt::color::orange_red), "{} \n", mode);
    switch (mode) {
      case LockMode::INTENTION_SHARED:
        if (confilct(mask, {LockMode::EXCLUSIVE})) {
          return false;
        }
        break;
      case LockMode::INTENTION_EXCLUSIVE:
        if (confilct(mask, {LockMode::SHARED_INTENTION_EXCLUSIVE, LockMode::SHARED, LockMode::EXCLUSIVE})) {
          return false;
        }
        break;
      case LockMode::SHARED:
        if (confilct(mask,
                     {LockMode::INTENTION_EXCLUSIVE, LockMode::SHARED_INTENTION_EXCLUSIVE, LockMode::EXCLUSIVE})) {
          return false;
        }
        break;
      case LockMode::SHARED_INTENTION_EXCLUSIVE:
        if (confilct(mask, {LockMode::INTENTION_EXCLUSIVE, LockMode::SHARED_INTENTION_EXCLUSIVE, LockMode::SHARED,
                            LockMode::EXCLUSIVE})) {
          return false;
        }
        break;
      case LockMode::EXCLUSIVE:
        if (mask != 0) {
          return false;
        }
        break;
    }
    mask |= 1 << static_cast<int>(mode);
    // fmt::print("{}\n", std::bitset<6>(mask).to_string());
  }
  return true;
}
```



#### 锁管理器怎么实现？

![img](https://pic4.zhimg.com/80/v2-12bde2c60081ddf8b60bd765cb55ebe7_720w.webp)

![image-20230701160645599](https://img2023.cnblogs.com/blog/2174405/202307/2174405-20230705203205342-15130024.png)

以上图为例，加锁操作流程是这样的：

- 如果事务 T99 请求对表 22 加 S 锁，直接把这个 request 添加到**队列末尾**
- 如果事务 T5 请求对标 22 加 X 锁，触发**锁升级**，把队列中原元素**删除**，并且将升级的锁请求插入到**第一个未授予的位置**，并重新检查该请求能否成功上锁。

解锁流程更简单的：（以解表锁为例）

- 解锁条件检查：**解表锁之前行锁是否清空**，LQ-Queue 有无该数据项的授权记录等
- 直接删除队列中的的 request
- 根据隔离级别**更新事务状态**



**详细的流程：**

lock manager处理锁请求流程：

看事务状态

获取lockqueue

看是不是锁升级（锁升级请求优先满足），是的话释放原来的锁

把锁请求加入请求队列

然后尝试获得锁。

- 当一个新的加锁请求到达时，如果**请求队列存在**，他在对应资源的请求队列**末尾**添加一条记录，否则创建一个新的队列。

- - 如果资源没有没有被锁住，那么授予锁。
  - 如果已经有事务获取了锁，检查锁的兼容性，如果**兼容并且先前锁请求都被授予，才能获取锁**，否则只能等待。**这里保证了锁的请求不会饥饿。**
  - 兼容的定义：和锁升级请求兼容，和其他前面的wait和所有已经获得的锁兼容。
  
- 当解锁请求到达时，lock manger把对应的加锁记录从请求队列中移除。检查后续等待获取请求能否被授予锁。



可重复读：            解开 s/x锁都应该将事务状态设置为  SHRINKING 

读提交：            **解锁 × 锁应将事务状态设置为 SHRINKING**            **解锁 s 锁不影响事务状态**。

 读未提交：            解锁 X 锁事务状态设置为 SHRINKING            **不允许使用 S 锁**            在此隔离级别下解锁S锁的行为未定义





#### 死锁检测

线程定期进行死锁检测。

* 遍历所有的 request_queue 建立一个 wait for queue
* 用 dfs 检测环
* 如果有环 找到 txn id 最大的**事务**
  * 设置为中止。
  * 唤醒 cv
  * 删除锁
  * 删除边
* 开始下一轮检测。直到找不到环。

```c++
std::unordered_map<txn_id_t, std::vector<txn_id_t>> waits_for_;

```



## 为什么用堆









#### 火山模型

##### 火山模型的优点

简单清晰，算子耦合度低。

##### 火山模型的缺点

数据以行为单位处理，不利于CPU cache发挥作用。

调用的 next() 为虚函数，开销大：

1. 内存开销：需要开辟虚函数表
2. 运行时开销：运行时需要查表
3. 优化限制：不能 inline



### SQL 优化

少用子查询

用in替代or

limit M, N -> where id > M, limit N

in 和 exist 区别



### 索引失效

最左匹配

索引列操作

类型转换

like % 以通配符开头



使用前缀索引



# RaftKV