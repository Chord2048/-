[toc]

# CMU15445

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



锁的种类

|        |      |      |
| ------ | ---- | ---- |
| S-LOCK |      |      |
| X-LOCK |      |      |
|        |      |      |





### 两阶段锁

2PL 用来决定事务能不能访问对象，不需要提前直到查询计划。

**Phase #1: Growing** → Each txn **requests** the locks that it needs from the DBMS’s lock manager. → The lock manager grants/denies lock requests. 

**Phase #2: Shrinking** → The txn is allowed to **only release/downgrad**e locks that it previously acquired. It cannot acquire new locks

两阶段锁可以防止依赖有向图有环。



但是有**级联中止**问题。

由于 T1 中止了，T2 在之前读到 T1 写入的数据，就是所谓的 “脏读”。为了保证整个 schedule 是 serializable，DBMS 需要在 T1 中止后将曾经读取过 T1 写入数据的其它事务中止，而这些中止可能进而使得其它正在进行的事务级联地中止，这个过程就是所谓的级联中止。



#### 2PL 存在的问题：

- 可能出现**级联回滚** —— 解决方案：**严格**二阶段封锁协议 (**Strict 2PL, S-2PL**)
  - 提交了才能释放互斥锁
- 可能出现**脏读** —— 解决方案：**强**二阶段封锁协议 (**Strong Strict 2PL, SS-2PL，又称 Rigorous 2PL**)
  - 提交了才能释放所有锁
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

1. **shrinking 阶段只允许加S IS锁**。不可重复读。
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

- 当一个新的加锁请求到达时，如果**请求队列存在**，他在对应资源的请求队列**末尾**添加一条记录，否则创建一个新的队列。

- - 如果资源没有没有被锁住，那么授予锁。
  - 如果已经有事务获取了锁，检查锁的兼容性，如果**兼容并且先前锁请求都被授予，才能获取锁**，否则只能等待。**这里保证了锁的请求不会饥饿。**

- 当解锁请求到达时，lock manger把对应的加锁记录从请求队列中移除。检查后续等待获取请求能否被授予锁。



可重复读：            解开 s/x锁都应该将事务状态设置为  SHRINKING 

读提交：            **解锁 × 锁应将事务状态设置为 SHRINKING**            **解锁 s 锁不影响事务状态**。

 读未提交：            解锁 X 锁事务状态设置为 SHRINKING            **不允许使用 S 锁**            在此隔离级别下解锁S锁的行为未定义



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