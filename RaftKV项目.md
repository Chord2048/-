# RAFT_KV

[toc]

本项目致力于构建一种基于Raft一致性算法的分布式键值存储数据库，以确保数据的一致性、可用性和分区容错性。



## 代码结构

### RAFT 设计

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

![img](https://article-images.zsxq.com/Fqql9dbJTAJS5EA6pby_AIIw7mVo)

1. leaderHeartBeatTicker 超时调用 doHeartBeat
   1. **leaderHearBeatTicker**:负责查看是否该发送心跳了，如果该发起就执行doHeartBeat。
   2. **doHeartBeat**:实际发送心跳
2. 对某个节点发送快照或者日志
   1. leader 发现 follower 节点的日志落后于自己的快照，那么要发送整个快照。

发送快照 - 调用 - 回复

1. **leaderSendSnapShot** ---RPC---> **InstallSnapshot**

发送日志 - 调用 - 回复

1. **sendAppendEntries**  ---RPC---> **AppendEntries** 



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

收到心跳，执行 `AppendEntries` 

1. 收到过期 leader 消息，返回无效的 nextindex 提醒leader
   1. **不用更新定时器**

2. 任期更大，更新自己的任期（三变）

3. 任期相同，更新自己为 Follower，不用重置投票。重置选举定时器。

4. 然后比较日志

   1. 如果 leader 前一条 commit 日志大于自己的最后一条， 返回 false，携带 nextindex = lastlogindex + 1

   2. （加入快照后）如果 prelogindex < m_lastSnapshotIncludeIndex，没跟上快照，返回false，携带 m_lastSnapshotIncludeIndex + 1

   3. 如果有 (PrelogTerm, PrelogIndex) 这一条日志，则进行复制。

      1. 在 LastLogIndex 之前的，看匹不匹配，不匹配就更新，相当于(DROP，然后换新的)
      2. 在 LastLogIndex 之后的，直接插入。
      3. 更新 `commitIndex = std::min(args->leadercommit(), getLastLogIndex())`
      4. 回复 success

   4. 不匹配。

      1. 一个一个往前。

      2. 或者，**优化加速**

         1. PrevLogIndex 长度合适，但是不匹配，因此**往前寻找** **矛盾的term的第一个元素** (把 prevIndex 的 term 所在的 log 都去掉)

            ```c++
            for (int index = args->prevlogindex(); index >= m_lastSnapshotIncludeIndex; --index) {
              if (getLogTermFromLogIndex(index) != getLogTermFromLogIndex(args->prevlogindex())) {
                reply->set_updatenextindex(index + 1); 
                  break;
              }
            }
            ```




### 持久化相关

持久化就是把不能丢失的数据保存到磁盘。

持久化的内容为两部分：

1. raft节点的部分信息
   1. term：：当前节点的Term，避免重复到一个Term，可能会遇到重复投票等问题。
   2. votedfor ： 当前Term给谁投过票，避免故障后重复投票。
   3. m_logs
2. **kvDb的快照**
   1. **m_lastSnapshotIncludeIndex** 快照最新包含的日志 Index
   2. **m_lastSnapshotIncludeTerm** 快照最新包含的日志 term

Snapshot是kvDb的快照，也可以看成是日志，因此:**全部的日志 = m_logs + snapshot**





**为什么 applyIndex 不用持久化**。

简答：重启后重置为0，并且会重新应用已经提交的日志恢复到正确的状态，RSM重新应用已经提交的日志是不会改变状态机的状态的。

在 Raft 协议中，`applyIndex` 是用来跟踪**已经被应用**到状态机的日志条目的最高索引。

然而，`applyIndex` 不需要持久化，主要有以下几个原因：

1. **重启后的安全性**：当一个节点重启后，它的 `applyIndex` 会被重置为 0。状态机是确定性的，所以重新应用已提交的日志条目不会改变状态机的状态。
2. **节省存储空间**：持久化数据需要存储空间。由于 `applyIndex` 可以在节点重启后被安全地重置，所以没有必要将其持久化，这可以节省存储空间。
3. **简化实现**：不需要持久化 `applyIndex` 可以简化 Raft 的实现。节点只需要在启动时重置 `applyIndex`，然后重新应用所有已提交的日志条目，就可以恢复到正确的状态。

总的来说，虽然 `applyIndex` 是一个重要的指标，但是由于其特性和 Raft 协议的设计，它不需要被持久化。



#### 为什么要持久化这些内容

两部分原因：**共识安全**、**优化**。

除了snapshot相关的部分，其他部分都是为了共识安全。

而snapshot是因为日志一个一个的叠加，会导致最后的存储非常大，因此使用snapshot来压缩日志。



#### 什么时候持久化

需要持久化的**内容发送改变**的时候就要注意持久化。

比如`term` 增加，日志增加等等。



#### 谁来调用持久化

仓库代码中选择的是raft类自己来完成持久化。因为raft类最方便感知自己的term之类的信息有没有变化。

注意，虽然持久化很耗时，但是持久化这些内容的时候不要放开锁，以防其他线程改变了这些值，导致其它异常。



#### 实现持久化

持久化需要考虑：**速度**、**大小**、**二进制安全**。

仓库实现目前采用的是使用 **boost** 库中的持久化实现，将需要持久化的数据序列化转成`std::string` 类型再写入磁盘。







##### protobuf 和 boost

**Protobuf**

Google Protocol Buffers (GPB)是Google内部使用的数据编码方式，旨在用来代替XML进行数据交换。可用于数据序列化与反序列化。主要特性有：

- 高效
- 语言中立(Cpp, Java, Python)
- 可扩展

**Boost.Serialization**

Boost.Serialization可以创建或重建程序中的等效结构，并保存为二进制数据、文本数据、XML或者有用户自定义的其他文件。该库具有以下吸引人的特性：

- 代码**可移植**（实现仅依赖于ANSI C++）。
- 深度指针保存与恢复。
- 可以**序列化STL容器和其他常用模版库**。
- 数据可移植。
- 非入侵性。

**对比**

- Google Protocol Buffers效率较高，但是**数据对象必须预先定义，并使用protoc编译**，适合要求效率，允许自定义类型的内部场合使用。
- Boost.Serialization **使用灵活简单，而且支持标准C++容器**。





### kvServer

![img](https://article-images.zsxq.com/Ft_negDcbLW-DMJbvTGxxGliTxCJ)

kvServer其实是个中间组件，负责沟通kvDB和raft节点。

#### KVServer 类定义

```c++
class KvServer : raftKVRpcProctoc::kvServerRpc {			// 继承自 protobuf 的 kvServerRpc
 private:
  std::mutex m_mtx;
  int m_me;
  std::shared_ptr<Raft> m_raftNode;					// 托管的 RAFT 类
  std::shared_ptr<LockQueue<ApplyMsg> > applyChan;  // kvServer和raft节点的通信管道
  int m_maxRaftState;                               // 最大可存的 log 数

  std::string m_serializedKVData;  // todo ： 序列化后的kv数据，理论上可以不用，但是目前没有找到特别好的替代方法
  SkipList<std::string, std::string> m_skipList;	// 跳表
  std::unordered_map<std::string, std::string> m_kvDB; // just unordered_map

  std::unordered_map<int, LockQueue<Op> *> waitApplyCh;
  // index(raft) -> chan  //？？？字段含义   waitApplyCh是一个map，键是int，值是Op类型的管道

  // 保存某个 Client 最后一个命令的 id，用于去重
  std::unordered_map<std::string, int> m_lastRequestId;  // clientid -> requestID  //一个kV服务器可能连接多个client
 

  // last SnapShot point , raftIndex
  int m_lastSnapShotRaftLogIndex;

 public:
  KvServer() = delete;

  KvServer(int me, int maxraftstate, std::string nodeInforFileName, short port);

  void StartKVServer();

  void DprintfKVDB();

  void ExecuteAppendOpOnKVDB(Op op);
  void ExecuteGetOpOnKVDB(Op op, std::string *value, bool *exist);
  void ExecutePutOpOnKVDB(Op op);

  void Get(const raftKVRpcProctoc::GetArgs *args,
           raftKVRpcProctoc::GetReply
               *reply);  //将 GetArgs 改为rpc调用的，因为是远程客户端，即服务器宕机对客户端来说是无感的
  /**
   * 從raft節點中獲取消息  （不要誤以爲是執行【GET】命令）
   * @param message
   */
  void GetCommandFromRaft(ApplyMsg message);

  bool ifRequestDuplicate(std::string ClientId, int RequestId);

  // clerk 使用 RPC 远程调用
  void PutAppend(const raftKVRpcProctoc::PutAppendArgs *args, raftKVRpcProctoc::PutAppendReply *reply);

  ////一直等待raft传来的applyCh
  void ReadRaftApplyCommandLoop();

  void ReadSnapShotToInstall(std::string snapshot);

  bool SendMessageToWaitChan(const Op &op, int raftIndex);

  // 检查是否需要制作快照，需要的话就向raft之下制作快照
  void IfNeedToSendSnapShotCommand(int raftIndex, int proportion);

  // Handler the SnapShot from kv.rf.applyCh
  void GetSnapShotFromRaft(ApplyMsg message);

  std::string MakeSnapShot();

 public:  // for rpc 重写虚函数
  void PutAppend(google::protobuf::RpcController *controller, const ::raftKVRpcProctoc::PutAppendArgs *request,
                 ::raftKVRpcProctoc::PutAppendReply *response, ::google::protobuf::Closure *done) override;

  void Get(google::protobuf::RpcController *controller, const ::raftKVRpcProctoc::GetArgs *request,
           ::raftKVRpcProctoc::GetReply *response, ::google::protobuf::Closure *done) override;

  /////////////////serialiazation start ///////////////////////////////
  // notice ： func serialize
 private:
  friend class boost::serialization::access;

  // When the class Archive corresponds to an output archive, the
  // & operator is defined similar to <<.  Likewise, when the class Archive
  // is a type of input archive the & operator is defined similar to >>.
  template <class Archive>
  void serialize(Archive &ar, const unsigned int version)  //这里面写需要序列话和反序列化的字段
  {
    ar &m_serializedKVData;

    // ar & m_kvDB;
    ar &m_lastRequestId;
  }

  std::string getSnapshotData() {
    m_serializedKVData = m_skipList.dump_file();
    std::stringstream ss;
    boost::archive::text_oarchive oa(ss);
    oa << *this;
    m_serializedKVData.clear();
    return ss.str();
  }

  void parseFromString(const std::string &str) {
    std::stringstream ss(str);
    boost::archive::text_iarchive ia(ss);
    ia >> *this;
    m_skipList.load_file(m_serializedKVData);
    m_serializedKVData.clear();
  }

  /////////////////serialiazation end ///////////////////////////////
};
```



#### kvServer怎么和上层kvDB沟通，怎么和下层raft节点沟通

通过这两个成员变量实现：

```c++
   std::shared_ptr<LockQueue<ApplyMsg> > applyChan; //kvServer和raft节点的通信管道
   std::unordered_map<std::string, std::string> m_kvDB; //kvDB，用unordered_map来替代 后面更新了
```

`LockQueue` 是一个并发安全的队列，这种方式其实是模仿的go中的 channel 机制。

#### KvServer 处理外部请求 

1. 接收外部请求。
2. 本机内部与raft和 kvDB 协商如何处理该请求。
3. 返回外部响应。

请求和返回的操作我们可以通过 http、自定义协议等等方式实现，选择使用 rpc 来实现。

#### Get 和 Put 请求

```c++
   void PutAppend(google::protobuf::RpcController *controller,
                   const ::raftKVRpcProctoc::PutAppendArgs *request,
                   ::raftKVRpcProctoc::PutAppendReply *response,
                   ::google::protobuf::Closure *done) override;
    void Get(google::protobuf::RpcController *controller,
             const ::raftKVRpcProctoc::GetArgs *request,
             ::raftKVRpcProctoc::GetReply *response,
             ::google::protobuf::Closure *done) override;
```

#### Raft 保证 Client 读写命令的线性一致性

即对于每个clinet，都有一个唯一标识，对于每个client，只执行递增的命令。

ClientID + CommandID 标识。

##### 线性一致性的写 KV

// TODO



### RPC怎么设计？

总体的特征: TCP 长连接,同步阻塞

### RPC Channel

// TODO 他这个 socket 有点垃圾

```c++
// 继承自 google::protobuf::RpcChannel

class MprpcChannel : public google::protobuf::RpcChannel {
 public:
  // 所有通过stub代理对象调用的rpc方法，都走到这里了，统一做rpc方法调用的数据数据序列化和网络发送 那一步
  // 重载 CallMethod
    void CallMethod(const google::protobuf::MethodDescriptor *method, google::protobuf::RpcController *controller,
                  const google::protobuf::Message *request, google::protobuf::Message *response,
                  google::protobuf::Closure *done) override;
  MprpcChannel(string ip, short port, bool connectNow);

 private:
  int m_clientFd;
  const std::string m_ip;  //保存ip和端口，如果断了可以尝试重连
  const uint16_t m_port;
    
  /// @brief 连接ip和端口,并设置m_clientFd 质朴的 socket 连接
  /// @param ip ip地址，本机字节序
  /// @param port 端口，本机字节序
  /// @return 成功返回空字符串，否则返回失败信息
  bool newConnect(const char *ip, uint16_t port, string *errMsg);
};

```

#### 日志压缩

采取快照（snapshot）的方式来压缩日志，有点像 rdb。



#### Raft 读写请求

Raft对只读操作的处理办法是

1. 只读请求最终也必须依靠Leader来执行，如果是Follower接收请求的，那么必须转发
2. **记录下当前日志的commitIndex => readIndex**
3. 执行读操作前要向集群广播一次心跳，并得到majority的反馈
4. **等待状态机的applyIndex移动过readIndex**
5. 通过**查询状态机**来执行读操作并返回客户端最终结果。



### CLEAK

```c++
int main(){
    Clerk client;			
    client.Init("test.conf");		// 连接所有的raftKvServer节点
    auto start = now();
    int count = 500;
    int tmp = count;
    while (tmp --){
        client.Put("x",std::to_string(tmp));		// 写 KEY, VALUE

        std::string get1 = client.Get("x");			// 读 KEY, VALUE
        std::printf("get return :{%s}\r\n",get1.c_str());  
    }
    return 0;
}
```

先看 CLERK 的定义

```c++
class Clerk {
 private:
  /**
   * 保存所有raft节点的fd
   * 每个 raftServerRpcUtil 里有一个 stub, 初始化时传入 ip 和 port
   **/
  std::vector<std::shared_ptr<raftServerRpcUtil>> m_servers;  
  std::string m_clientId;
  int m_requestId;
  int m_recentLeaderId;  //只是有可能是领导

  std::string Uuid() {
    return std::to_string(rand()) + std::to_string(rand()) + std::to_string(rand()) + std::to_string(rand());
  }  //用于返回随机的clientId ? 这个随机不够优雅。

  //    MakeClerk  todo
  void PutAppend(std::string key, std::string value, std::string op);

 public:
  //对外暴露的三个功能和初始化
  void Init(std::string configFileName);
  std::string Get(std::string key);
  void Put(std::string key, std::string value);
  void Append(std::string key, std::string value);

 public:
  Clerk();
};
```

读的时候调用这个，通过 RaftServerRpcUtil 里的stub 调用 get

```c++
// Clerk.cpp
std::string Clerk::Get(std::string key) {
  m_requestId++;
  auto requestId = m_requestId;
  int server = m_recentLeaderId;
    
  // 设置 RPC 参数
  raftKVRpcProctoc::GetArgs args;
  args.set_key(key);
  args.set_clientid(m_clientId);
  args.set_requestid(requestId);

  while (true) {
    raftKVRpcProctoc::GetReply reply;
      
    // 
    bool ok = m_servers[server]->Get(&args, &reply);
    if (!ok ||
        reply.err() ==
            ErrWrongLeader) {  //会一直重试，因为requestId没有改变，因此可能会因为RPC的丢失或者其他情况导致重试，kvserver层来保证不重复执行（线性一致性）
      server = (server + 1) % m_servers.size();
      continue;
    }
    if (reply.err() == ErrNoKey) {
      return "";
    }
    if (reply.err() == OK) {
      m_recentLeaderId = server;
      return reply.value();
    }
  }
  return "";
}
```

```c++
// raftServerRpcUtil.cpp
bool raftServerRpcUtil::Get(raftKVRpcProctoc::GetArgs *GetArgs, raftKVRpcProctoc::GetReply *reply) {
  MprpcController controller;
  // 这是 protobuf 生成的一个虚函数
  stub->Get(&controller, GetArgs, reply, nullptr);
  return !controller.Failed();
}

// protobuf 生成的 kvServerRpc.pb.cc
void kvServerRpc_Stub::Get(::PROTOBUF_NAMESPACE_ID::RpcController* controller,
                              const ::raftKVRpcProctoc::GetArgs* request,
                              ::raftKVRpcProctoc::GetReply* response,
                              ::google::protobuf::Closure* done) {
  channel_->CallMethod(descriptor()->method(1),
                       controller, request, response, done);
}

// mprpcchannel.cpp
// 调用 channel 的 CallMethod
// header_size + service_name method_name args_size + args
// 所有通过stub代理对象调用的rpc方法，都会走到这里了，
// 统一通过rpcChannel来调用方法
// 统一做rpc方法调用的数据数据序列化和网络发送
void MprpcChannel::CallMethod(const google::protobuf::MethodDescriptor* method,
                              google::protobuf::RpcController* controller, const google::protobuf::Message* request,
                              google::protobuf::Message* response, google::protobuf::Closure* done) {
  
    
    
  // 如果没连接，尝试连接
  if (m_clientFd == -1) {
    std::string errMsg;
    bool rt = newConnect(m_ip.c_str(), m_port, &errMsg);
    if (!rt) {
      DPrintf("[func-MprpcChannel::CallMethod]重连接ip：{%s} port{%d}失败", m_ip.c_str(), m_port);
      controller->SetFailed(errMsg);
      return;
    } else {
      DPrintf("[func-MprpcChannel::CallMethod]连接ip：{%s} port{%d}成功", m_ip.c_str(), m_port);
    }
  }

  const google::protobuf::ServiceDescriptor* sd = method->service();
  std::string service_name = sd->name();     // service_name
  std::string method_name = method->name();  // method_name

  // 获取参数的序列化字符串长度 args_size
  uint32_t args_size{};
  std::string args_str;
  if (request->SerializeToString(&args_str)) {
    args_size = args_str.size();
  } else {
    controller->SetFailed("serialize request error!");
    return;
  }
    
  // 序列化 RPC 首部
  // 首部包含 service_name, method_name, 和参数长度
  RPC::RpcHeader rpcHeader;
  rpcHeader.set_service_name(service_name);
  rpcHeader.set_method_name(method_name);
  rpcHeader.set_args_size(args_size);

  std::string rpc_header_str;
  if (!rpcHeader.SerializeToString(&rpc_header_str)) {
    controller->SetFailed("serialize rpc header error!");
    return;
  }

  // 使用protobuf的CodedOutputStream来构建发送的数据流
  std::string send_rpc_str;  // 用来存储最终发送的数据
    
    
  {
    // 创建一个StringOutputStream用于写入send_rpc_str
    google::protobuf::io::StringOutputStream string_output(&send_rpc_str);
    google::protobuf::io::CodedOutputStream coded_output(&string_output);

    // 先写入header的长度（变长编码）
    coded_output.WriteVarint32(static_cast<uint32_t>(rpc_header_str.size()));

    // 不需要手动写入header_size，因为上面的WriteVarint32已经包含了header的长度信息
    // 然后写入rpc_header本身
    coded_output.WriteString(rpc_header_str);
  }

  // 最后，将请求参数附加到send_rpc_str后面
  send_rpc_str += args_str;

  // 发送rpc请求
  // 这里的 send 是同步阻塞的？
  // 失败会重试连接再发送，重试连接失败会直接 return
  while (-1 == send(m_clientFd, send_rpc_str.c_str(), send_rpc_str.size(), 0)) {
    char errtxt[512] = {0};
    sprintf(errtxt, "send error! errno:%d", errno);
    std::cout << "尝试重新连接，对方ip：" << m_ip << " 对方端口" << m_port << std::endl;
    close(m_clientFd);
    m_clientFd = -1;
    std::string errMsg;
    bool rt = newConnect(m_ip.c_str(), m_port, &errMsg);
    if (!rt) {
      controller->SetFailed(errMsg);
      return;
    }
  }
    
  /*
  从时间节点来说，这里将请求发送过去之后rpc服务的提供者就会开始处理，返回的时候就代表着已经返回响应了
  */
  // 接收rpc请求的响应值
  char recv_buf[1024] = {0};
  int recv_size = 0;
  if (-1 == (recv_size = recv(m_clientFd, recv_buf, 1024, 0))) {
    close(m_clientFd);
    m_clientFd = -1;
    char errtxt[512] = {0};
    sprintf(errtxt, "recv error! errno:%d", errno);
    controller->SetFailed(errtxt);
    return;
  }

  // 反序列化rpc调用的响应数据
  // std::string response_str(recv_buf, 0, recv_size); //
  // bug：出现问题，recv_buf中遇到\0后面的数据就存不下来了，导致反序列化失败 if
  // (!response->ParseFromString(response_str))
  if (!response->ParseFromArray(recv_buf, recv_size)) {
    char errtxt[1050] = {0};
    sprintf(errtxt, "parse error! response_str:%s", recv_buf);
    controller->SetFailed(errtxt);
    return;
  }
}
```

看 raftcore Main() 这里，这里


```c++
// example/raftKVDB.cpp
  for (int i = 0; i < nodeNum; i++) {
    short port = startPort + static_cast<short>(i);
    std::cout << "start to create raftkv node:" << i << "    port:" << port << " pid:" << getpid() << std::endl;
    pid_t pid = fork();  // 创建新进程
    if (pid == 0) {
      // 子进程的代码
      auto kvServer = new KvServer(i, 500, configFileName, port);
      pause();  // 子进程进入等待状态，不会执行 return 语句
    } else if (pid > 0) {
      // 如果是父进程
      // 父进程的代码
      sleep(1);
    } else {
      // 如果创建进程失败
      std::cerr << "Failed to create child process." << std::endl;
      exit(EXIT_FAILURE);
    }
  }
```

new 一个 KvServer 

* 会启动一个线程，注册发服务，阻塞阻塞等待远程的 RPC 调用
* 创建并且初始化一个 Raft 类，Raft 类继承自 RaftPRC 并且重写了其中的 RPC 调用虚函数，同时开启了三个 ticker 线/协程。
* 连接其他的节点
* 初始化一个 KVDB
* 启动一个线程，阻塞监听 raft node 的 apply 消息

```c++
// 需要的参数：id, 最大 log 数量, 配置文件位置, 端口号
KvServer::KvServer(int me, int maxraftstate, std::string nodeInforFileName, short port) : m_skipList(6) {
  std::shared_ptr<Persister> persister = std::make_shared<Persister>(me);

  m_me = me;
  m_maxRaftState = maxraftstate;
  applyChan = std::make_shared<LockQueue<ApplyMsg> >();
  
  // 每个 KVServer 和一个 Raft 节点以及 KVDB 联系
  m_raftNode = std::make_shared<Raft>();
    
  ////////////////clerk层面 kvserver开启rpc接受功能
  //    同时raft与raft节点之间也要开启rpc功能，因此有两个注册
  std::thread t([this, port]() -> void {
    // provider是一个rpc网络服务对象。把UserService对象发布到rpc节点上
    RpcProvider provider;
    provider.NotifyService(this);
    provider.NotifyService(
        this->m_raftNode.get());  // todo：这里获取了原始指针，后面检查一下有没有泄露的问题 或者 shareptr释放的问题
    // 启动一个rpc服务发布节点   Run以后，进程进入阻塞状态，等待远程的rpc调用请求
    provider.Run(m_me, port);
  });
  t.detach();

  ////开启rpc远程调用能力，需要注意必须要保证所有节点都开启rpc接受功能之后才能开启rpc远程调用能力
  ////这里使用睡眠来保证
  std::cout << "raftServer node:" << m_me << " start to sleep to wait all ohter raftnode start!!!!" << std::endl;
  // TODO magic number
  sleep(6);
  std::cout << "raftServer node:" << m_me << " wake up!!!! start to connect other raftnode" << std::endl;
  
  //获取所有raft节点ip、port ，并进行连接  ,要排除自己
  MprpcConfig config;
  config.LoadConfigFile(nodeInforFileName.c_str());
  std::vector<std::pair<std::string, short> > ipPortVt;
  for (int i = 0; i < INT_MAX - 1; ++i) {
    std::string node = "node" + std::to_string(i);

    std::string nodeIp = config.Load(node + "ip");
    std::string nodePortStr = config.Load(node + "port");
    if (nodeIp.empty()) {
      break;
    }
    ipPortVt.emplace_back(nodeIp, atoi(nodePortStr.c_str()));  //沒有atos方法，可以考慮自己实现
  }
  std::vector<std::shared_ptr<RaftRpcUtil> > servers;
  //进行连接
  for (int i = 0; i < ipPortVt.size(); ++i) {
    if (i == m_me) {
      servers.push_back(nullptr);
      continue;
    }
    std::string otherNodeIp = ipPortVt[i].first;
    short otherNodePort = ipPortVt[i].second;
    auto *rpc = new RaftRpcUtil(otherNodeIp, otherNodePort);
    servers.push_back(std::shared_ptr<RaftRpcUtil>(rpc));

    std::cout << "node" << m_me << " 连接node" << i << "success!" << std::endl;
  }
  sleep(ipPortVt.size() - me);  //等待所有节点相互连接成功，再启动raft
  m_raftNode->init(servers, m_me, persister, applyChan);
  // kv的server直接与raft通信，但kv不直接与raft通信，所以需要把ApplyMsg的chan传递下去用于通信，两者的persist也是共用的

  //////////////////////////////////
  // KVDB 相关

  // You may need initialization code here.
  // m_kvDB; //kvdb初始化
  m_skipList;
  waitApplyCh;
  m_lastRequestId;
  m_lastSnapShotRaftLogIndex = 0;  // todo:感覺這個函數沒什麼用，不如直接調用raft節點中的snapshot值？？？
  auto snapshot = persister->ReadSnapshot();
  if (!snapshot.empty()) {
    ReadSnapShotToInstall(snapshot);
  }
  std::thread t2(&KvServer::ReadRaftApplyCommandLoop, this);  //马上向其他节点宣告自己就是leader
  t2.join();  //由於ReadRaftApplyCommandLoop一直不會結束，达到一直卡在这的目的
}
```

TODO 接下来呢？



### KV存储

基于跳表

跳表是一种动态数据结构，支持快速的插入、删除、查找操作，**时间复杂度都是 O(logn) 。** **跳表的空间复杂度是 O(n)** 。

#### 跳表怎么实现

##### 节点定义

```c++
// Class template to implement node
template <typename K, typename V>
class Node {
 public:
  Node() {}
  Node(K k, V v, int);
  ~Node();

  K get_key() const;
  V get_value() const;
  void set_value(V);
  // Linear array to hold pointers to next node of different level
  Node<K, V> **forward;

  int node_level;
 private:
  K key;
  V value;
};

// Ctor
template <typename K, typename V>
Node<K, V>::Node(const K k, const V v, int level) {
  this->key = k;
  this->value = v;
  this->node_level = level;

  // level + 1, because array index is from 0 - level
  this->forward = new Node<K, V> *[level + 1];
  // Fill forward array with 0(NULL)
  memset(this->forward, 0, sizeof(Node<K, V> *) * (level + 1));
};

```

##### 跳表类定义

```c++
template <typename K, typename V>
class SkipList {
 public:
  SkipList(int maximum_level);
  ~SkipList();
  int get_random_level();				// 产生一个 level
  Node<K, V> *create_node(K, V, int);	// key, value, level
  int insert_element(K, V);				// 插入
  void display_list();
  bool search_element(K, V &value);		// 搜索		
  void delete_element(K);				// 删除
  void insert_set_element(K &, V &);	// 插入
  std::string dump_file();				// 落盘
  void load_file(const std::string &dumpStr);	// 加载
  
  //递归删除节点
  void clear(Node<K, V> *);
  int size();

 private:
  void get_key_value_from_string(const std::string &str, std::string *key, std::string *value);
  bool is_valid_string(const std::string &str);

 private:
  // Maximum level of the skip list
  int _max_level;
  // current level of skip list
  int _skip_list_level;
  // pointer to header node
  Node<K, V> *_header;

  // file operator
  std::ofstream _file_writer;
  std::ifstream _file_reader;

  // skiplist current element count
  int _element_count;
  std::mutex _mtx;  // mutex for critical section
};


// ctor
// construct skip list
template <typename K, typename V>
SkipList<K, V>::SkipList(int max_level) {
  this->_max_level = max_level;
  this->_skip_list_level = 0;
  this->_element_count = 0;
  // create header node and initialize key and value to null
  K k;
  V v;
  // 创建一个 header
  this->_header = new Node<K, V>(k, v, _max_level);
};
```

##### 节点有多少层?

```c++
// p = 0.5
template <typename K, typename V>
int SkipList<K, V>::get_random_level() {
  int k = 1;
  while (rand() % 2) {
    k++;
  }
  k = (k < _max_level) ? k : _max_level;
  return k;
};
```

##### Search

```c++
template <typename K, typename V>
bool SkipList<K, V>::search_element(K key, V &value) {
  Node<K, V> *current = _header;

  // start from highest level of skip list
  for (int i = _skip_list_level; i >= 0; i--) {  // 再向下
    while (current->forward[i] && current->forward[i]->get_key() < key) { // 先向右
      current = current->forward[i];
    }
  }
    
  // current 到达了底层最后一个小于 key 的节点
  // reached level 0 and advance pointer to right node, which we search
  current = current->forward[0];	

  // if current node have key equal to searched key, we get it
  if (current && current->get_key() == key) {
    value = current->get_value();
    std::cout << "Found key: " << key << ", value: " << current->get_value() << std::endl;
    return true;
  }

  std::cout << "Not Found Key:" << key << std::endl;
  return false;
}
```

Insert

```c++
template <typename K, typename V>
int SkipList<K, V>::insert_element(const K key, const V value) {
  _mtx.lock();
  Node<K, V> *current = this->_header;
  // create update array and initialize it
  // update is array which put node that the node->forward[i] should be operated later
  Node<K, V> *update[_max_level + 1];
  memset(update, 0, sizeof(Node<K, V> *) * (_max_level + 1));

  // start form highest level of skip list
  for (int i = _skip_list_level; i >= 0; i--) {	// 再向下
    while (current->forward[i] != NULL && current->forward[i]->get_key() < key) { // 先向右
      current = current->forward[i];
    }
    update[i] = current; // 每一层,找到最后一个小于 key 的位置
  }

  // reached level 0 and forward pointer to right node, which is desired to insert key.
  // 插入位置
  current = current->forward[0];

  // if current node have key equal to searched key, we get it
  if (current != NULL && current->get_key() == key) {
    std::cout << "key: " << key << ", exists" << std::endl;
    _mtx.unlock();
    return 1;
  }

  // if current is NULL that means we have reached to end of the level
  // if current's key is not equal to key that means we have to insert node between update[0] and current node
  if (current == NULL || current->get_key() != key) {
    // Generate a random level for node
    int random_level = get_random_level();
    // If random level is greater thar skip list's current level, initialize update value with pointer to header
    if (random_level > _skip_list_level) {
      for (int i = _skip_list_level + 1; i < random_level + 1; i++) {
        update[i] = _header;
      }
      _skip_list_level = random_level;
    }

    // create new node with random level generated
    Node<K, V> *inserted_node = create_node(key, value, random_level);

    // insert node
    for (int i = 0; i <= random_level; i++) {
      inserted_node->forward[i] = update[i]->forward[i];
      update[i]->forward[i] = inserted_node;
    }
    std::cout << "Successfully inserted key:" << key << ", value:" << value << std::endl;
    _element_count++;
  }
  _mtx.unlock();
  return 0;
}
```

删除元素

```c++
// Delete element from skip list
template <typename K, typename V>
void SkipList<K, V>::delete_element(K key) {
  _mtx.lock();
  Node<K, V> *current = this->_header;
  Node<K, V> *update[_max_level + 1];
  memset(update, 0, sizeof(Node<K, V> *) * (_max_level + 1));

  // start from highest level of skip list
  // update 记录某一层前一个节点
  for (int i = _skip_list_level; i >= 0; i--) {
    while (current->forward[i] != NULL && current->forward[i]->get_key() < key) {
      current = current->forward[i];
    }
    update[i] = current;
  }
	
  // current 指向删除位置
  current = current->forward[0];
  if (current != NULL && current->get_key() == key) {
    // start for lowest level and delete the current node of each level
    for (int i = 0; i <= _skip_list_level; i++) {
      // if at level i, next node is not target node, break the loop.
      if (update[i]->forward[i] != current) break;
      update[i]->forward[i] = current->forward[i];
    }

    // Remove levels which have no elements 这里要删除空的层数
    while (_skip_list_level > 0 && _header->forward[_skip_list_level] == 0) {
      _skip_list_level--;
    }
    delete current;
    _element_count--;
  }
  _mtx.unlock();
  return;
}
```



#### 什么时候落盘

#### 落盘文件和快照怎么配合？



## 一些实现细节

1. 用 LockQueue 模拟 go chan 和 kv server 联系

2. 防御性编程，随时 assert。

3. 跟着论文写代码，判断一个边界情况。

4. 得到的票数用 make_shared 保存

5. 线程传入一个类方法时，不但需要穿方法的地址，还要传类的指针 (T* 或者 this)

6. 随时检查任期

7. 更改任期的时候，要立刻重置投票 （同时三变）

8. ```C++
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

9. 考虑请求丢失的情况，可能requestvote响应时，已经有 rf.votedFor == args.CandidateId

10. leader收到心跳回复后，就算term相同也不一定成功了。可能发送时 term 小于 follower ，但在回复前 term 更新了。

11. 在commit的时候，我们只能够commit `entryTerm == rf.currentTerm` 的`LogEntry`

12. 收到follower的拒绝后，要立即重发新的请求。

13. 要做好幂等性。

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



有待学习的东西：

protobuf

boost 序列化

rpc

跳表

linux 网络编程

## 面试可能的问题

### RAFT各种场景和应对方法

##### 什么是网络分区，怎么解决

##### 为什么xxx要持久化，为什么xxx不用持久化

持久化是为了共识安全和优化。

不用持久化是因为不会破坏共识安全，重启后可以恢复到原来的状态。减少持久化可以节省空间，简化设计。就这个系统而言，每次三变后（term，votedfor，status）都要持久化。



ApplyIndex 持久化的好处

是的，`applyIndex` 可以被持久化，尽管在 Raft 协议的原始设计中并没有这样做。将 `applyIndex` 持久化有一些潜在的好处：

1. **减少重启后的计算量**：如果 `applyIndex` 被持久化，那么当节点重启后，它就不需要重新应用所有已提交的日志条目到状态机。这可以减少重启后的计算量，特别是当日志条目数量很大，或者状态机的操作很复杂时。
2. **避免重复执行副作用**：有些状态机的操作可能有副作用，例如发送网络请求或写入磁盘文件。如果 `applyIndex` 被持久化，那么这些操作就不会在节点重启后被重复执行。



##### 说一下宕机恢复的过程







##### 为什么raft里leader要同时保存nextidex和matchindex数组，只用 nextIndex 可以吗

`nextIndex[]` 数组帮助 Leader 知道下一步应该发送哪个日志条目给 Follower。

`matchIndex[]` 数组则用于跟踪已经成功复制到 Follower 的日志条目的最高索引。这对于 Leader 来说也是必要的，因为 Leader 需要知道何时可以安全地提交（即应用到状态机）一个日志条目。

第一次变成 leader，nextIndex 会变成自己的 lastlog + 1，matchindex则初始化为 0。



`m_nextIndex` 与`m_matchIndex` 是否有冗余，即使用一个`m_nextIndex` 可以吗？

显然是不行的，`m_nextIndex` 的作用是用来寻找`m_matchIndex` ，不能直接取代。我们可以从这两个变量的变化看，在当选leader后，`m_nextIndex` 初始化为最新日志index，`m_matchIndex` 初始化为0，如果日志不匹配，那么`m_nextIndex` 就会不断的缩减，直到遇到匹配的日志，这时候`m_nextIndex` 应该一直为`m_matchIndex+1` 。

### RPC相关

##### RPC用的什么IO模型？



### 一致性理论

##### 什么是CAP



### 共识算法相关

#### 有没有对性能测试

单个客户端三个节点垃圾虚拟机上跑了测试

**只读操作也必须经过majority确认** <- 网络分区

put 120 puts/s

get 180 gets/s

基本都是 RPC 用的时间,阻塞在了 recv 和 send 上.

### 性能优化

有空读一下这个

https://juejin.cn/post/6906508138124574728#heading-1

#### 读优化

##### Read Index

与 Raft Log Read 相比，Read Index **省掉了同步 log 的开销**，能够**大幅提升**读的**吞吐**，**一定程度上降低**读的**时延**。其大致流程为：

1. Leader 在收到客户端读请求时，记录下当前的 commit index，称之为 **read index**。
2. Leader 向 followers 发起一次心跳包，这一步是为了确保领导权，避免网络分区时少数派 leader 仍处理请求。
3. 等待状态机**至少**应用到 read index（即 apply index **大于等于** read index）。
4. 执行读请求，将状态机中的结果返回给客户端。

这里第三步的 apply index **大于等于** read index 是一个关键点。因为在该读请求发起时，我们将当时的 commit index 记录了下来，只要使客户端读到的内容在该 commit index 之后，那么结果**一定都满足线性一致**（如不理解可以再次回顾下前文线性一致性的例子以及2.2中的问题一）。

只发心跳确认leader身份, 而不用等新的 log 的 apply



1. 如果当前任期内有状态未确认的 LogEntry，先等它提交。假如该 Leader 刚上任且有状态未确认的 LogEntry，则尝试同步一个空 LogEntry 以更新 commit index。
2. 经过了第一步，便可以将当前的 commit index 暂存起来。
3. 触发一轮新的心跳，如果这轮心跳达成共识，说明：**至少到发送心跳的那个时间点，该节点都是唯一一个被认可的 Leader，所以在那个时间点前暂存的最新 commit index，一定也是整个集群中最新的**！
4. 1-3步确认了一个最新的 commit index，等待这个 index 及其以前的 LogEntry 都应用到状态机。
5. 这样一来，在**发送心跳前**的只读操作，都可以正常执行并且返回结果，它们读到的一定是最新的。（注意这里的“最新的”是从只读请求发起的时间点来定义的，只要完成 1-4 步，即使在读状态机时，有新的 Leader 产生了并且在上面有了新的写入操作，都不会影响这些读操作的线性一致性）

可以看到上面的步骤虽然保证了 Leader 节点上读的线性一致性，但是如果在从节点上进行读取，还是可能会有不一致的问题。但其实解决方案也非常简单：**从节点在收到读请求时，向主节点“索要”当前时间的最新 commit index** 即可，主节点收到这个“索要”请求后，只需执行 1-3 步便可得到这个 commit index。



##### Lease Read

 进一步**省去了网络交互开销**，因此更能**显著降低**读的**时延**。

基本思路是 leader 设置一个**比选举超时（Election Timeout）更短的时间作为租期**，在租期内我们可以相信其它节点一定没有发起选举，集群也就一定不会存在脑裂，所以在这个时间段内我们直接读主即可，而非该时间段内可以继续走 Read Index 流程，Read Index 的心跳包也可以为租期带来更新。

leader发送RPC的时候，会首先记录一个时间点 **start**，当系统大部分节点都回复哪个RPC时，我们就可以认为 leader 的 **lease 有效期可以到 start + election timeout + clock drift bound（正负未知）**这个时间点。因为 follower 至少在 election timeout的时间之后才会重新发生选举，所以正常来讲这套机制是有效的，


Lease Read 可以认为是 Read Index 的时间戳版本，额外依赖时间戳会为算法带来一些不确定性，如果时钟发生漂移会引发一系列问题，因此需要谨慎的进行配置。

当leader持有lease时，leader认为此时其为合法的leader，因此可以直接将其*commit index*作为*read index*。后续的处理流程与**ReadIndex**相同。





##### Follower Read

在前边两种优化方案中，无论我们怎么折腾，核心思想其实只有两点：

- **保证在读取时的最新 commit index 已经被 apply。**
- **保证在读取时 leader 仍拥有领导权。**

这两个保证分别对应2.2节所描述的两个问题。

其实无论是 **Read Index** 还是 **Lease Read**，最终目的都是为了解决第二个问题。换句话说，读请求最终一定都是由 leader 来承载的。

那么读 follower 真的就不能满足线性一致吗？其实不然，这里我们给出一个可行的读 follower 方案：**Follower 在收到客户端的读请求时，向 leader 询问当前最新的 commit index，反正所有日志条目最终一定会被同步到自己身上，follower 只需等待该日志被自己 commit 并 apply 到状态机后，返回给客户端本地状态机的结果即可**。这个方案叫做 **Follower Read**。

注意：Follower Read 并不意味着我们在读过程中完全不依赖 leader 了，在保证线性一致性的前提下完全不依赖 leader 理论上是不可能做到的。

#### PreVote

避免分区的时候 term 暴增。

引入 **PerVote机制** 解决问题，简单来讲就是在身份转化为Candidate之前引入一个新的状态，即 **PreCandidate**，它的作用是**发送一个预选举包**，其中Term为自身Term+1，注意现在**结点真实Term并不增加**，在收**到大多数结点的回复以后才进行真正的选举，此时也真正的自增Term。**

选举开销多了一轮消息广播。但是 leader 宕机不常见。







### 测试相关

##### 有没有对性能进行过测试？用的什么工具？怎么测试的？

**回答要点：** perf火焰图。

**示例回答：**

这里回答一下**火焰图**的基本使用就差不多了，如果大家没有使用过的话推荐大家去看篇博文入门了解基本操作和原理（探针），

我这里给出一个初步的结果，如果只有一个客户端：**并发几十**，大部分的损耗在 RPC 这边。多个客户端的结果没有测试。

现在的测试还比较简单，在五个 raft 集群上测。

### 存储相关

##### 为什么跳表

* 范围查询
* 实现简单,调整稳定可以调整层数参数
* 

##### 跳表的对比

##### 跳表修改？

##### 有什么组件用了跳表

##### 了解LSM吗
