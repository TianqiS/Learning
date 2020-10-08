### Raft算法小结

#### raft算法的几个部分

1. leader election
2. log replication
3. safety
4. state space reduction

#### raft算法的特性

1. Strong leader

   Raft uses a stronger form of leader- ship than other consensus algorithms. For example, log entries only flow from the leader to other servers. This simplifies the management of the replicated log and makes Raft easier to understand.

2. Leader election

   Raft uses **randomized timers to elect leaders**. This adds only a small amount of mechanism to the heartbeats already required for any consensus algorithm, while resolving conflicts sim- ply and rapidly.

3. Membership changes

   Raft’s mechanism for changing the set of servers in the cluster uses a **new *joint consensus* approach where the majorities of two different configurations overlap during transitions.** This allows the cluster to continue operating normally during configuration changes.

#### Repliacted stated machines



<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gjhpvxvqbgj30lw0h6gog.jpg" alt="截屏2020-10-08 上午10.29.07" style="zoom:50%;" />

复制状态机可以保证在一个集群上的机器绝大多数的状态是相同的，比较适合解决允许适当容错率的问题

#### Raft算法的简化操作

raft算法相对于Paxos算法来说更容易理解，这是因为raft算法在设计的时候在两个技术方面做了考虑

1. the first technique is the well-known approach of problem decomposition.
2. second approach was to simplify the state space by reducing the number of states to consider, making the system more coherent and eliminating nondeterminism where possible. 

#### the properties that Raft guarantees

1. **Election Safety**:给定一个term最多只能选举一个leader
2. **Leader Append-Only**:一个leader从来不重写或删除它的日志，但是可以添加新的
3. **Log Matching**：if two logs contain an entry with the same index and term, then the logs are identical in all entries up through the given index.
4. **Leader Completeness**：if a log entry is committed in a given term, then that entry will be present in the logs of the leaders for all higher-numbered terms.
5. **State Machine Safety（最重要的）**：if a server has applied a log entry at a given index to its state machine, no other server will ever apply a different log entry for the same index

#### Leader election

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gjhu7czb4yj30mo07ugma.jpg" alt="截屏2020-10-08 下午12.58.32" style="zoom:50%;" />

如上图所示，每当选出一个新的leader，term的值就会加1，但是如果在竞选leader的时候发生了冲突，term的值也会增加，开启下一轮的竞选，无论如何和，**Raft保证一个term里面最多有一个leader。**

term的值一直在更换：

- 如果server的term比其他的要小，它将会把自己的term更新到更大的值
- 如果一个candidate或者leader发现自己的term过期了，那么它将会转化为follower的状态

**如果一个Server收到一个过期的term，那么它将拒绝这次的request。**

当follower想要election成为leader的时候：

1. 首先增加自己的term值，同时将state转化为cadidate

2. 为自己投票同时将**RequestVote RPCs**并行地发送给在集群中的其他servers

3. 接下来可能会发生三种情况：

   - 赢得选举

   - 另一个server赢得了选举

   - 没有winner，重新开始下一轮的选举

     在该candiate发起投票的时候还有其他candidate也发起了投票，并且产生了split votes这种现象，这种情况比较危险，如果不加上适当的措施的话有可能会一直重复下去，因此raft采用了一种randomized election timeouts来保证split votes很少发生

     > To prevent split votes in the first place, election timeouts are chosen randomly from a fixed interval (e.g., 150–300ms). This spreads out the servers so that in most cases only a single server will time out; it wins the election and sends heartbeats before any other servers time out.

如果candidate在等待投票的时候，收到了**AppendEntries RPC**，会用RPC中的term和自身的term进行对比：

- 如果RPC中的term和自身的一样或者比自身的大那么candidate会辩证follower的状态
- 如果RPC中的term没有自身的大，那么candidate会拒绝RPC，继续保持candidate的状态

#### Log replication

当一个leader收到来自client的command的时候，它将会执行如下步骤：

1. The leader appends the command to its log as a new entry

2. then issues AppendEntries RPCs in parallel to each of the other servers to replicate the entry

3. When the entry has been safely replicated (as described below), the leader applies the entry to its state machine and returns the result of that execution to the client.

   其中raft算法会保证每个committed entries会被可执行的状态机执行

在这之中，如果followers挂了或者运行比较慢再或者网络丢包了，leader会重复发RPC(即使已经向client回复了)，直到所有的followers最终存储了所有的entries

当一个entry被绝大多数的servers所复制的时候，leader会对该entry进行committed操作，同时，

> This also commits all preceding entries in the leader’s log, including entries created by previous leaders.

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gji0s2kbb0j30oy0ju77p.jpg" alt="截屏2020-10-08 下午4.45.58" style="zoom:50%;" />

如上图所示，The leader keeps track of the highest index it knows to be committed, and it includes that index in future AppendEntries RPCs (including heartbeats) so that the other servers eventually find out. Once a follower learns that a log entry is committed, it applies the entry to its local state machine (in log order).

**来自AppendEntries的一致性检查**(上面的补充)

> When send- ing an AppendEntries RPC, the leader includes the index and term of the entry in its log that immediately precedes the new entries. If the follower does not find an entry in its log with the same index and term, then it refuses the new entries. The consistency check acts as an induction step: the initial empty state of the logs satisfies the Log Matching Property, and the consistency check preserves the Log Matching Property whenever logs are extended. As a result, whenever AppendEntries returns successfully, the leader knows that the follower’s log is identical to its own log up through the new entries.

简单来讲就是followers会检查来自appendEntries的上一个index和term是否匹配

----

上述的一致性保证均是在leader保持正常的情况下得到保证的，但是如果leader挂了，可能会导致日志不一致的问题，下图展示了followers有可能和新的leader不同：

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gji1ek576bj30p20h8jtv.jpg" alt="截屏2020-10-08 下午5.07.37" style="zoom:50%;" />

在raft中，leader采用**强行复制的方法**来解决上述不一致性的问题：

1. leader首先要找出follower与它哪里不一致：
   - leader通过发送RPCs来发现不一致点
   - leader保存着每个follower的*nextIndex*，保存这个信息的原因是leader会根据这个信息来决定下一个发送给follower的entry
   - When a leader first comes to power, it initializes all nextIndex values to the index just after the last one in its log
   - If a follower’s log is inconsistent with the leader’s, the AppendEntries consis- tency check will fail in the next AppendEntries RPC.
   - After a rejection, the leader decrements nextIndex and retries the AppendEntries RPC. Eventually nextIndex will reach a point where the leader and follower logs match
   - When this happens, AppendEntries will succeed, which removes any conflicting entries in the follower’s log and appends entries from the leader’s log
2. 之后删除不一致点之后的日志
3. 发送不一致点之后的leader的日志

#### 安全机制

##### 选举的限制

在这一小节中将介绍Raft算法中的一种限制，限制了什么样的server才可以成为leader

> The restriction ensures that the leader for any given term con- tains all of the entries committed in previous terms

Raft使用投票机制来保证candidate拥有所有的committed entries，candidate的日志as least  **up-to-date** as any other log in that majority，那么该candidate就拥有了所有的committed entries

上述机制可以通过RPC来进行实现：

> the RPC includes information about the candidate’s log, and the voter denies its vote if its own log is more up-to-date than that of the candidate.

Up-to-date的比较方法：

1. 比较两者的term和index
2. 如果term不一样，那么term比较大的一方更加up-to-date
3. 如果term一样，那么index更大的更大up-to-date

##### Committing entries from previous terms

如果一个leader在commit的时候挂了，后面的leader会尝试完成entry的复制，However, a leader cannot im- mediately conclude that an entry from a previous term is committed once it is stored on a majority of servers.下图说明了一种问题，即使旧的entry被大多数servers存储了，但是仍有可能被新的leader覆盖：

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gji3jgwlnjj30ss0dm40w.jpg" alt="截屏2020-10-08 下午6.21.34" style="zoom:50%;" />

<img src="https://tva1.sinaimg.cn/large/007S8ZIlgy1gji3v1c5oij30o60hqtca.jpg" alt="截屏2020-10-08 下午6.32.40" style="zoom:50%;" />

为了避免上述现象，raft只提交当前leader的term，一旦当前term被committed了，前面的entries也会被间接提交，这是由于**Log Matching Property**

