# RAFT_KV

[toc]

争取两周做完！

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

send_requestVote

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

##### requestvote

void Raft::RequestVote( const mprrpc::RequestVoteArgs *args, mprrpc::RequestVoteReply *reply)

* 请求包含当前任期、参选号、最后的log任期和idx
* 如果任期比自己小，说明出现网络分区，竞选者 outofdate。
* 如果任期比自己大，三变











## 一些实现细节

1. 用 LockQueue 模拟 go chan 和 kv server 联系
2. 防御性编程，随时 assert。
3. 得到的票数用 make_shared 保存
4. 线程传入一个类方法时，不但需要穿方法的地址，还要传类的指针 (T* 或者 this)
5. 随时检查任期

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





