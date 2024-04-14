# RAFT_KV

[toc]

本项目致力于构建一种基于Raft一致性算法的分布式键值存储数据库，以确保数据的一致性、可用性和分区容错性。



## 代码结构

### Raft.h Raft.cpp | RAFT节点

主要有 3/4 个部分

* raft 主要流程
  * 选举 sendRequestVote RequestVote
  * 日志同步
  * 心跳 sendAppendEntries & AppendEntries
* 定时器 三个独立的定时器
  * 定时应用到状态机 appliedTicker
  * 心跳定时 leaderHeartBeatTicker
  * 选举超时定时器 electionTimeOutTicker
* 持久化
  * persist : votedfor currentTerm Log
  * 易失的不用持久化
    * leader 其他节点nextid，matchid
    * 全部服务器 commitid，appliedid
* 新加的 TODO Snapshot

#### 初始化

#### 超时选举调用过程

1. 当 **electionTimeOutTicker** 响应，调用 doElection
2. 启动多线程/协程进行调用 sendRequetVote
3. RPC 远程调用，其他节点调用 RequestVote
4. 其他节点 RPC 回复 Leader，决定是否投票



##### ElectionTimeOutTiker 定时器设计

1. 随机一个超时时间，减去距离上一次重置定时器的时间
2. 睡眠
3. 如果睡眠期间没有重置定时器，发起选举



##### 开始选举

1. 变为 candidate 任期自增，投票给自己
2. 记录票数 = 1
3. 重置定时器
4. 发布 request vote
5. 开启线程调用 send_requestVote(request, reply)

##### send_requestVote

* 没收到回复，返回false

* 收到回复检查任期
  * 回复任期更大，三变，返回。
  * 回复任期更小 ？异常
* 投票结果
  * 没投，返回
  * 投了，检查有没有超半数
* 超半数了，变成leader
  * 初始化nextIndex, matchindex
    * m_nextIndex[i] = lastLogIndex + 1
    * m_matchIndex[i] = 0;  // 这里每次换 leader 都要更新
  * 发送心跳，宣告胜选
  * 持久化

##### requestvote 响应

void Raft::RequestVote( const mprrpc::RequestVoteArgs *args, mprrpc::RequestVoteReply *reply)

* 请求包含当前任期、参选号、最后的log任期和idx
* 如果任期比自己小，说明出现网络分区，竞选者 outofdate。
* 如果任期比自己大，三变，！**重置投票**
* 任期一致（三变后一致，或者没有三变就一致了）
  * 检查日志 Candidate 的日志是不是比接收者的日志新
    * candidate 最后日志的 term 更大
    * 或者 term 一样，index 更大

  * 检查是不是投过票了
    * votedfor == -1？
    * votedfor == candidateID？(请求丢失重发)

* 同意投票



#### 日志复制 | 心跳

1. leaderHeartBeatTicker 超时调用 doHeartBeat
   1. **leaderHearBeatTicker**:负责查看是否该发送心跳了，如果该发起就执行doHeartBeat。
   2. **doHeartBeat**:实际发送心跳
2. 对某个节点发送快照或者日志
   1. leader 发现 follower 节点的日志落后于自己的快照，那么要发送整个快照。

发送快照 - 调用 - 回复

1. **leaderSendSnapShot** ---RPC---> **AppendEntries** 

发送日志 - 调用 - 回复

1. **sendAppendEntries**  ---RPC---> **InstallSnapshot**



AppendEntriesRPC 请求的内容包括：

1. 当前leader的任期
2. leader的id
3. **prevLogIndex** 添加下面log的前一个log的Index。正常情况下，follower的 lastIndex 应该等于prevLogIndex。
4. **PrevLogTerm** 前一条log的term。和prevLogIndex一起用的，因为**可能过期的leader之前给这个follower发送过一些未apply的消息**，所以仅仅靠preLogIndex是无法确认leader之前的消息和follower当前最后的消息是一样的。**而 prevLogTerm 和 prevLogIndex 都一样，则状态肯定是一致的。**
5. entries [] log：要新增加的log门，为了提升数据同步的效率，所以可能**一次性发送多条log**，这样提高了写的速度。

回复包括：

1. 返回的 term
2. true or false
   1. 如果**follower的term比自己小**，那么follower会更新自己的term为leader Term，并且返回leader Term，但是**会返回false**。
   2. 如果follower的term和自己一样大，也有可能**数据不够**而返回false，这个时候需要**递减leader的nextIndex和matchIndex**，最终实现数据一致性。
3. 还有 nextindex 告诉 leader 需要哪条日志





##### Raft::doHeartBeat()

对除自己的节点：

m_nextIndex[i] <= m_lastSnapshotIncludeIndex 发送快照

否则，发送心跳

先获取 preindex 和 prevterm

```c++
//获取本次发送的一系列日志的上一条日志的信息，以判断是否匹配
// SNAP_SHOT | LOGS
// 日志index不是物理index，应该减去 lastsnapshotindex

getPrevLogInfo(i, &preLogIndex, &PrevLogTerm);  
```

如果preindex不等于快照最后的index （不是 LOG 里的第一条），发送prev后面的 log

否则 发送所有 log

（这里需要转换成物理的索引）

启动线程发送 RPC 并接收请求

重置心跳时间



##### SendAppendEntries 

通常是leader调用

doheartBeat 开启多线程调用 SendAppendEntries

发 RPC，接受回复

1. 上锁
2. 检查TERM，如果任期更大就三变返回
3. 重新检查是不是 Leader
4. 如果日志不匹配 （回复为 false）更新 nextindex
5. 否则，更新 matchindex[] 和 nextindex[]
   1. matchindex 更新为 prevlogindex + entrise_size
   2. nnextindex = matchindex + 1
6. 检查能不能 commit
   1. appendNums (返回true的节点数) 大于一半，说明可以commit
   2. 重置 appendnums = 0 保证幂等性 （有很多线程在跑这个函数）
   3. **安全性检查**
      1. 当前的term有日志提交，才会进行提交
         1. raft无法保证之前term的Index是否提交
         2. 保证**领导人完备性**：当选领导人的节点**拥有之前被提交的所有log**，当然也可能有一些**没有被提交的**
      2. 也就是 被超过半数节点接受的最后一条 entries 的term 要等于当前的 term
      3. 更新 commitIndex 



##### AppendEntries 收到心跳的响应函数

1. 收到过期 leader 消息，返回无效nextindex 提醒leader
   1. 不用更新定时器
2. 



### RPC怎么设计？

#### 日志压缩

## 一些实现细节

1. 用 LockQueue 模拟 go chan 和 kv server 联系

2. 防御性编程，随时 assert。

3. 得到的票数用 make_shared 保存

4. 线程传入一个类方法时，不但需要穿方法的地址，还要传类的指针 (T* 或者 this)

5. 随时检查任期

6. 更改任期的时候，要立刻重置投票 （同时三变）

7. ```C++
   // C++ 实现 defer
   template <typename Function>
   struct Defer {
       Function f;
       Defer(Function f) : f(f) {}
       ~Defer() { f(); }
   };
   
   #define DEFER_1(x, y) x##y
   #define DEFER_2(x, y) DEFER_1(x, y)
   #define DEFER_0(x)    DEFER_2(x, __COUNTER__)
   #define make_defer(...) Defer DEFER_0(_deferred_option){__VA_ARGS__}
   
   ```

8. 考虑请求丢失的情况，可能requestvote响应时，已经有 rf.votedFor == args.CandidateId

9. leader收到心跳回复后，就算term相同也不一定成功了。可能发送时 term 小于 follower ，但在回复前 term 更新了。

### 项目的难点

1. 边界情况很多，网络/分布式系统复杂。测试不好复现。
   1. assert
   2. log
   3. 针对性测试
   4. 脑中走一遍流程
   5. 防御性编程
   6. 及时三变
2. 



## 可能的改进？

三个part

### RPC

### Raft 一致性算法

### 存储引擎 跳表KV



中间件 protobuf



### TODO

1. 协程相关
2. 日志相关
3. benchmark
4. 日志寻找匹配加速



## 面试可能的问题

### RAFT各种场景和应对方法

##### 什么是网络分区，怎么解决

##### 为什么xxx要持久化，为什么xxx不用持久化

##### 说一下宕机恢复的过程



### RPC相关

##### RPC用的什么IO模型？

##### 

### 一致性理论

##### 什么是CAP



### 共识算法相关



### 存储相关

##### 为什么跳表

##### 跳表的对比

##### 跳表修改？

##### 有什么组件用了跳表

##### 了解LSM吗
