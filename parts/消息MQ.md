- [首页](../README.md "Java Interview Docs")
1. ActiveMQ
   - 连接：客户端通过 ConnectionFactory 与 Broker 建立连接。 
   - 生产：生产者创建消息并指定一个 Destination（Queue 或 Topic），发送至 Broker。 
   - 存储/转发：Broker 接收消息。若是 Queue，则存入持久化数据库（如 KahaDB）并寻找一个活跃消费者；若是 Topic，则分发给当前在线的所有订阅者。 
   - 消费：消费者监听对应的 Destination。消费完成后向 Broker 发送确认（ACK）。
2. RabbitMQ
   - RabbitMQ 中通过 Binding(绑定) 将 Exchange(交换器) 与 Queue(消息队列) 关联起来，在绑定的时候一般会指定一个 BindingKey(绑定建) ,这样 RabbitMQ 就知道如何正确将消息路由到队列了
   - 生产者将消息发送给交换器时，需要一个 RoutingKey,当 BindingKey 和 RoutingKey 相匹配时，消息会被路由到对应的队列中。
3. RabbitMQ 核心概念
   - 在 RabbitMQ 中，消息并不是直接被投递到 Queue(消息队列) 中的，中间还必须经过 Exchange(交换器) 这一层，Exchange(交换器) 会把我们的消息分配到对应的 Queue(消息队列) 中。
   - 生产者将消息发给交换器的时候，一般会指定一个 RoutingKey(路由键)，用来指定这个消息的路由规则，而这个 RoutingKey 需要与交换器类型和绑定键(BindingKey)联合使用才能最终生效。
4. RocketMQ
   - Topic（主题）：消息的一级分类。例如“订单消息”、“支付消息”。
   - MessageQueue（消息队列）：Topic 的物理分区。为了提高并发，一个 Topic 会被拆分成多个 MessageQueue 存储在不同的 Broker 上。
   - Tag（标签）：消息的二级分类。用于在同一 Topic 下过滤不同类型的消息（如 Order_Pay 和 Order_Cancel）。
   - NameServer：路由注册中心（类似于微服务里的 Eureka/Nacos）。管理 Broker 的地址和 Topic 的路由信息。
   - Broker：实际存储消息和处理请求的核心服务器。
5. Kafka：
   - Topic（主题） : Producer 将消息发送到特定的主题，Consumer 通过订阅特定的 Topic(主题) 来消费消息。
   - Partition（分区） : Partition 属于 Topic 的一部分。一个 Topic 可以有多个 Partition ，并且同一 Topic 下的 Partition 可以分布在不同的 Broker 上，这也就表明一个 Topic 可以横跨多个 Broker。
6. 如何保证 RabbitMQ 高可用的
    - 镜像集群模式：每个 RabbitMQ 节点都有这个 queue 的一个完整镜像，包含 queue 的全部数据的意思。然后每次你写消息到 queue 的时候，都会自动把消息同步到多个实例的 queue 上
7. 镜像集群模式 (Mirrored Queues) 的核心原理
   - 消息写入：当生产者发布一条消息到 Master 时，Master 会将该消息广播给所有的 Slave。只有当所有的 Slave 都收到并处理了该消息，Master 才会向生产者发送确认（Confirm）。
   - 消息读取：消费者无论连接哪个节点，最终都是从 Master 读取。Master 处理完读取后，会通知所有 Slave 移除对应的消息。
8. 仲裁队列（Quorum Queues）的核心思想
   - 仲裁队列不再使用老旧的镜像同步机制，而是完全基于 Raft 一致性协议。
   - 客户端请求：生产者发送一条持久化消息给 Leader。 
   - 预写日志 (Log Append)：Leader 将消息写入自己的本地磁盘（入日志），但此时消息状态是“未提交”。 
   - 并行广播：Leader 将这条日志记录同步发给所有的 Follower。 
   - 多数确认 (Quorum Reach)：只要 超过半数 的节点（包括 Leader 自己）反馈“已写入磁盘”，Leader 就认为这条消息已经安全。 
   - 提交与确认 (Commit & Ack)： 
     - Leader 将消息标记为“已提交”。 
     - Leader 向生产者返回 Publisher Confirm。 
     - 消息此时对消费者可见。
9. Kafka 如何保证消息的消费顺序
   - 每次添加消息到 Partition(分区) 的时候都会采用尾加法，如上图所示。 Kafka 只能为我们保证 Partition(分区) 中的消息有序。发送消息的时候指定 key/Partition，这样同一个 key 的消息可以保证只发送到同一个 partition，这个我们可以采用表/对象的 id 来作为 key 。
10. Kafka 如何保证消息不丢失
    - 消息在被追加到 Partition(分区)的时候都会分配一个特定的偏移量（offset）。偏移量（offset)表示 Consumer 当前消费到的 Partition(分区)的所在的位置。手动关闭自动提交 offset，每次在真正消费完消息之后再自己手动提交 offset 。
11. kafka 出现消息重复消费的原因
    - 服务端侧已经消费的数据没有成功提交 offset（根本原因），比如你刚刚消费完消息之后，还没提交 offset，结果自己挂掉了，那么这个消息理论上就会被消费两次
12. Kafka 如何保证消息不重复消费
    - 消费消息服务做幂等校验，比如 Redis 的 set、MySQL 的主键等天然的幂等功能。这种方法最有效
13. 什么是死信队列？如何导致的
    - 当消息在一个队列中变成死信 (dead message) 之后，它能被重新发送到另一个交换器中，这个交换器就是 DLX，绑定 DLX 的队列就称之为死信队列。（消息被拒、消息 TTL 过期、队列满了）
14. 几种消息队列的对比
    - ActiveMQ 的社区算是比较成熟，但是较目前来说，ActiveMQ 的性能比较差，而且版本迭代很慢，不推荐使用，已经被淘汰了。目前Apache已经推出AMQ的下一代：Artemis，基于 HornetQ 重新编写的内核，性能与高并发能力已能与顶级 MQ 叫板
    - RabbitMQ 在吞吐量方面虽然稍逊于 Kafka、RocketMQ 和 Pulsar，但是由于它基于 Erlang 开发，所以并发能力很强，性能极其好，延时很低，达到微秒级。但是也因为 RabbitMQ 基于 Erlang 开发，所以国内很少有公司有实力做 Erlang 源码级别的研究和定制。如果业务场景对并发量要求不是太高（十万级、百万级），那这几种消息队列中，RabbitMQ 或许是你的首选。
    - RocketMQ 和 Pulsar 支持强一致性，对消息一致性要求比较高的场景可以使用。RocketMQ 阿里出品，Java 系开源项目，源代码我们可以直接阅读，然后可以定制自己公司的 MQ，并且 RocketMQ 有阿里巴巴的实际业务场景的实战考验。
    - Kafka 的特点其实很明显，就是仅仅提供较少的核心功能，但是提供超高的吞吐量，ms 级的延迟，极高的可用性以及可靠性，而且分布式可以任意扩展。极致的性能：基于 Scala 和 Java 语言开发，设计中大量使用了批量处理和异步的思想，最高可以每秒处理千万级别的消息。同时 Kafka 最好是支撑较少的 topic 数量即可，保证其超高吞吐量。Kafka 唯一的一点劣势是有可能消息重复消费，那么对数据准确性会造成极其轻微的影响，在大数据领域中以及日志采集中，这点轻微影响可以忽略这个特性天然适合大数据实时计算以及日志收集。如果是大数据领域的实时计算、日志采集等场景，用 Kafka 是业内标准的，绝对没问题，社区活跃度很高，绝对不会黄，何况几乎是全世界这个领域的事实性规范。
15. RabbitMQ 的事务机制局限于生产者和 RabbitMQ 之间，无法覆盖到消费者，也没有 Kafka 和 RocketMQ 中的全局事务支持
16. 在 Kafka 或 RocketMQ 中，使用事务消息模式时，通常是生产者的本地事务与消息发送的原子性，而不是与消费者的本地事务同时绑定。可以使用Kafka+saga模式确保在消费者的本地事务失败时通过补偿机制回滚前面的操作。
17. 延迟消息实现方案
    - RabbitMQ：通过 TTL (Time To Live) + DLX (Dead Letter Exchange)
      - 普通队列 (Order_Queue)：设置 x-message-ttl 为 1800000ms (30分钟)。消息到期后不被消费，会变成“死信”。 
        - 需为每一个常用的延迟等级创建一个“等待队列”（RabbitMQ 的队列是 FIFO（先进先出）的。如果一个队列里既有 30 分钟的消息，又有 10 分钟的消息，且 30 分钟的消息排在前面。即使 10 分钟的消息到期了，它也不会立即进入死信队列，必须等到前面的 30 分钟消息被处理（过期）后，后面的消息才会被检查。）
      - 死信交换机 (DLX)：将上述队列绑定到对应死信交换机
        - 可以为每个业务队列指定不同的 DLX，也可以让所有业务队列共用一个 DLX，通常建议按业务维度或逻辑维度定义 DLX
      - 死信队列 (Cancel_Order_Queue)：真正执行关单逻辑的消费者监听这个队列
        - DLX 可以绑定多个死信队列，且可以根据 Routing Key 精确投递
    - RabbitMQ Delayed Message Plugin（官方推荐插件）：引入了一种新的交换机类型 x-delayed-message。消息发送到交换机后，不会立即投递到队列，而是在交换机的“存储层”暂存，等时间到了再投递
      - 它利用了 Erlang 语言内置的一个名为 Mnesia 的数据库，当消息带有 x-delay 头时，插件会将消息体序列化后存储在 Exchange 所在节点的 Mnesia 表中
      - 每条消息的延迟时间可以由生产者动态指定（在 Header 里设置 x-delay），不需要为 10min、30min 分开建队列，一个交换机搞定
      - 数据安全隐患：消息在被投递前，是存在节点本地的 Mnesia 里的。RabbitMQ 的高可用队列（Mirrored Queues）保护的是队列里的数据，而不是 Exchange 里的数据。如果这个节点还没来得及把 Mnesia 的数据刷到磁盘就宕机了，或者磁盘损坏了，那这些还没到期的消息就彻底丢了。
      - 性能天花板：Mnesia 并不是为海量数据设计的。它在处理大量并发写入和过期扫描时，性能远不如专业的存储引擎。如果你的业务量巨大，延迟消息堆积成山，这个插件的性能会急剧下降，甚至拖垮整个 RabbitMQ 节点。所以，高并发、大数据量场景下，严禁使用此方案。
    - Kafka：并不原生支持“延迟消息”或“死信队列”机制。
      - 这与它的底层架构有关：Kafka 的 Partition 是一个顺序追加的日志文件，如果允许某条消息在中间“延迟”消费，会极大地破坏其顺序读取的高效性
      - 不支持消息级别 TTL，它只有 Topic 级别的数据保留策略
      - 没有原生死信队列，通常是由 消费者端（Consumer） 捕获异常后，手动将消息发送到一个特定的 _DLQ Topic 中实现
      - 工业级较常用的方案，利用外部存储（如 Redis 或 MySQL）做“中转站”
        - 纯 Redis ZSet，不结合Kafka 会有风险：
          - 缺乏消息确认（ACK）机制：获取到过期任务后，该任务就从 Redis 删除了。如果业务代码在处理关单逻辑时突然宕机或报错，这个订单就永远不会被自动关闭了
          - 缺乏负载均衡与消费者组概念：多个服务实例去轮询 Redis，你需要自己写 Lua 脚本来保证原子性（防止多个实例同时取到同一个订单）
      - 如果利用MySQL做“中转站”，性能瓶颈问题
        - 可以利用 MySQL 的分区表特性，按时间维度（比如按小时、按天）对表进行物理分区。这样在扫描时，只需要扫当前时间片的分区，效率极高。对于过期的历史分区，可以直接使用 DROP PARTITION，这比逐行 DELETE 快无数倍，还不会产生磁盘碎片
        - 可以采用“表轮转（Table Rotation）”的策略，准备两张表：tab_0（奇数日） 和 tab_1（偶数日），每日切换的时候TRUNCATE
        - 批量写入+扫描时，通常会使用乐观锁抢占任务
        - 最后的方案是读写分类、分库分表等
    - RocketMQ：原生支持“定时消息/延迟消息”，无需配置复杂的死信队列
      - 5.0 为了支持任意精度的定时，设计了两个核心文件
      - TimerWheel（时间轮）：一个固定大小的内存环形数组，数组的每个元素代表一个时间刻度（槽位），每个槽位存储一个指向 TimerLog 的物理偏移量，指针随系统时间转动。当指针转到某个槽位时，它会取出里面记录的 TimerLog 链表起始位置
      - TimerLog（定时日志）：顺序追加的日志文件（类似于 CommitLog），采用了顺序写（Sequential Write），这是磁盘性能最优的写入方式。