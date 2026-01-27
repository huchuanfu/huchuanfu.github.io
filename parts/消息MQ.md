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
4. RabbitMQ中交换机类型
   - Direct：生产者发送消息时带上一个 Routing Key，交换机会寻找与其完全一致的 Binding Key 的队列
   - Fanout：它不看任何 Key，只要队列绑定到了这个交换机，它就会给每个队列发一份消息拷贝
   - Topic：它在 Direct 的基础上增加了通配符功能，* (星号)：匹配一个单词，# (井号)：匹配零个或多个单词
   - Headers：完全无视 Routing Key。路由逻辑基于消息属性中的 headers 表
5. RocketMQ
   - Topic（主题）：消息的一级分类。例如“订单消息”、“支付消息”。
   - MessageQueue（消息队列）：Topic 的物理分区。为了提高并发，一个 Topic 会被拆分成多个 MessageQueue 存储在不同的 Broker 上。
   - Tag（标签）：消息的二级分类。用于在同一 Topic 下过滤不同类型的消息（如 Order_Pay 和 Order_Cancel）。
   - NameServer：路由注册中心（类似于微服务里的 Eureka/Nacos）。管理 Broker 的地址和 Topic 的路由信息。
   - Broker：实际存储消息和处理请求的核心服务器。
6. Kafka：
   - Topic（主题） : Producer 将消息发送到特定的主题，Consumer 通过订阅特定的 Topic(主题) 来消费消息。
   - Partition（分区） : Partition 属于 Topic 的一部分。一个 Topic 可以有多个 Partition ，并且同一 Topic 下的 Partition 可以分布在不同的 Broker 上，这也就表明一个 Topic 可以横跨多个 Broker。
7. 如何保证 RabbitMQ 高可用的
    - 镜像集群模式（已废弃）：早期的 RabbitMQ 高可用方案。每个节点都有 queue 的完整镜像。但由于性能损耗大且网络分区处理能力弱，RabbitMQ 3.8+ 以后官方已废弃该模式，推荐使用仲裁队列（Quorum Queues）。
    - 仲裁队列（Quorum Queues）：基于 Raft 协议的新一代高可用队列，数据安全性更高，网络开销更小。
8. 镜像集群模式 (Mirrored Queues) 的核心原理
   - 消息写入：当生产者发布一条消息到 Master 时，Master 会将该消息广播给所有的 Slave。只有当所有的 Slave 都收到并处理了该消息，Master 才会向生产者发送确认（Confirm）。
   - 消息读取：消费者无论连接哪个节点，最终都是从 Master 读取。Master 处理完读取后，会通知所有 Slave 移除对应的消息。
9. 仲裁队列（Quorum Queues）的核心思想
   - 仲裁队列不再使用老旧的镜像同步机制，而是完全基于 Raft 一致性协议。
   - 客户端请求：生产者发送一条持久化消息给 Leader。 
   - 预写日志 (Log Append)：Leader 将消息写入自己的本地磁盘（入日志），但此时消息状态是“未提交”。 
   - 并行广播：Leader 将这条日志记录同步发给所有的 Follower。 
   - 多数确认 (Quorum Reach)：只要 超过半数 的节点（包括 Leader 自己）反馈“已写入磁盘”，Leader 就认为这条消息已经安全。 
   - 提交与确认 (Commit & Ack)： 
     - Leader 将消息标记为“已提交”。 
     - Leader 向生产者返回 Publisher Confirm。 
     - 消息此时对消费者可见。
10. RabbitMQ 的懒队列（Lazy Queues）：消息进入队列后，立即写入磁盘，只有在消费者真正需要时才加载进内存。（需要rabbitmqctl set_policy Lazy手动开启）
    - 普通队列：积压 100 万条消息，可能占用 2GB 内存 -> 触发流控。
    - 懒队列：积压 100 万条消息，可能仅占用几十 MB 内存 -> 生产者可以继续飞速发送，直到磁盘写满。
11. Kafka 如何保证消息的消费顺序
    - 每次添加消息到 Partition(分区) 的时候都会采用尾加法，如上图所示。 Kafka 只能为我们保证 Partition(分区) 中的消息有序。发送消息的时候指定 key/Partition，这样同一个 key 的消息可以保证只发送到同一个 partition，这个我们可以采用表/对象的 id 来作为 key 。
12. Kafka 如何保证消息不丢失
    - 消息在被追加到 Partition(分区)的时候都会分配一个特定的偏移量（offset）。偏移量（offset)表示 Consumer 当前消费到的 Partition(分区)的所在的位置。手动关闭自动提交 offset，每次在真正消费完消息之后再自己手动提交 offset 。
13. kafka 出现消息重复消费的原因
    - 服务端侧已经消费的数据没有成功提交 offset（根本原因），比如你刚刚消费完消息之后，还没提交 offset，结果自己挂掉了，那么这个消息理论上就会被消费两次
14. Kafka 如何保证消息不重复消费
    - 消费消息服务做幂等校验，比如 Redis 的 set、MySQL 的主键等天然的幂等功能。这种方法最有效
15. 什么是死信队列？如何导致的
    - 当消息在一个队列中变成死信 (dead message) 之后，它能被重新发送到另一个交换器中，这个交换器就是 DLX，绑定 DLX 的队列就称之为死信队列。（消息被拒、消息 TTL 过期、队列满了）
16. 几种消息队列的对比
    - ActiveMQ 的社区算是比较成熟，但是较目前来说，ActiveMQ 的性能比较差，而且版本迭代很慢，不推荐使用，已经被淘汰了。目前Apache已经推出AMQ的下一代：Artemis，基于 HornetQ 重新编写的内核，性能与高并发能力已能与顶级 MQ 叫板
    - RabbitMQ 在吞吐量方面虽然稍逊于 Kafka、RocketMQ 和 Pulsar，但是由于它基于 Erlang 开发，所以并发能力很强，性能极其好，延时很低，达到微秒级。但是也因为 RabbitMQ 基于 Erlang 开发，所以国内很少有公司有实力做 Erlang 源码级别的研究和定制。如果业务场景对并发量要求不是太高（十万级、百万级），那这几种消息队列中，RabbitMQ 或许是你的首选。
    - RocketMQ 和 Pulsar 支持强一致性，对消息一致性要求比较高的场景可以使用。RocketMQ 阿里出品，Java 系开源项目，源代码我们可以直接阅读，然后可以定制自己公司的 MQ，并且 RocketMQ 有阿里巴巴的实际业务场景的实战考验。
    - Kafka 的特点其实很明显，就是仅仅提供较少的核心功能，但是提供超高的吞吐量，ms 级的延迟，极高的可用性以及可靠性，而且分布式可以任意扩展。极致的性能：基于 Scala 和 Java 语言开发，设计中大量使用了批量处理和异步的思想，最高可以每秒处理千万级别的消息。同时 Kafka 对 Topic 数量相对敏感（相比 RocketMQ），过多的 Topic 会导致随机 I/O 增加从而影响吞吐量。Kafka 唯一的一点劣势是有可能消息重复消费，那么对数据准确性会造成极其轻微的影响，在大数据领域中以及日志采集中，这点轻微影响可以忽略这个特性天然适合大数据实时计算以及日志收集。如果是大数据领域的实时计算、日志采集等场景，用 Kafka 是业内标准的，绝对没问题，社区活跃度很高，绝对不会黄，何况几乎是全世界这个领域的事实性规范。
17. RabbitMQ 的事务机制局限于生产者和 RabbitMQ 之间，无法覆盖到消费者，也没有 Kafka 和 RocketMQ 中的全局事务支持
18. 在 Kafka 或 RocketMQ 中，使用事务消息模式时，通常是生产者的本地事务与消息发送的原子性，而不是与消费者的本地事务同时绑定。可以使用Kafka+saga模式确保在消费者的本地事务失败时通过补偿机制回滚前面的操作。
19. 延迟消息实现方案
    - **RabbitMQ**：通过 TTL (Time To Live) + DLX (Dead Letter Exchange)
      - 普通队列 (Order_Queue)：设置 x-message-ttl 为 1800000ms (30分钟)。消息到期后不被消费，会变成“死信”。 
        - 需为每一个常用的延迟等级创建一个“等待队列”（RabbitMQ 的队列是 FIFO（先进先出）的。如果一个队列里既有 30 分钟的消息，又有 10 分钟的消息，且 30 分钟的消息排在前面。即使 10 分钟的消息到期了，它也不会立即进入死信队列，必须等到前面的 30 分钟消息被处理（过期）后，后面的消息才会被检查。）
      - 死信交换机 (DLX)：将上述队列绑定到对应死信交换机
        - 可以为每个业务队列指定不同的 DLX，也可以让所有业务队列共用一个 DLX，通常建议按业务维度或逻辑维度定义 DLX
      - 死信队列 (Cancel_Order_Queue)：真正执行关单逻辑的消费者监听这个队列
        - DLX 可以绑定多个死信队列，且可以根据 Routing Key 精确投递
    - **RabbitMQ Delayed Message Plugin（官方推荐插件）**：引入了一种新的交换机类型 x-delayed-message。消息发送到交换机后，不会立即投递到队列，而是在交换机的“存储层”暂存，等时间到了再投递
      - 它利用了 Erlang 语言内置的一个名为 Mnesia 的数据库，当消息带有 x-delay 头时，插件会将消息体序列化后存储在 Exchange 所在节点的 Mnesia 表中
      - 每条消息的延迟时间可以由生产者动态指定（在 Header 里设置 x-delay），不需要为 10min、30min 分开建队列，一个交换机搞定
      - 数据安全隐患：消息在被投递前，是存在节点本地的 Mnesia 里的。RabbitMQ 的高可用队列（Mirrored Queues）保护的是队列里的数据，而不是 Exchange 里的数据。如果这个节点还没来得及把 Mnesia 的数据刷到磁盘就宕机了，或者磁盘损坏了，那这些还没到期的消息就彻底丢了。
      - 性能天花板：Mnesia 并不是为海量数据设计的。它在处理大量并发写入和过期扫描时，性能远不如专业的存储引擎。如果你的业务量巨大，延迟消息堆积成山，这个插件的性能会急剧下降，甚至拖垮整个 RabbitMQ 节点。所以，高并发、大数据量场景下，严禁使用此方案。
    - **Kafka**：并不原生支持“延迟消息”或“死信队列”机制。
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
        - 最后的方案是读写分离、分库分表等
    - **RocketMQ**：原生支持“定时消息/延迟消息”，无需配置复杂的死信队列
      - 5.0 为了支持任意精度的定时，设计了两个核心文件
      - TimerWheel（时间轮）：一个固定大小的内存环形数组，数组的每个元素代表一个时间刻度（槽位），每个槽位存储一个指向 TimerLog 的物理偏移量，指针随系统时间转动。当指针转到某个槽位时，它会取出里面记录的 TimerLog 链表起始位置
      - TimerLog（定时日志）：顺序追加的日志文件（类似于 CommitLog），采用了顺序写（Sequential Write），这是磁盘性能最优的写入方式。
20. Kafka 和 RocketMQ 的零拷贝技术 (Zero Copy)
    - [RocketMQ 为什么性能不如 Kafka？](https://zhuanlan.zhihu.com/p/1994073653223112762)
    - **核心背景**：传统的数据传输（如读取文件发送网络）需要 4 次上下文切换（用户态<->内核态）和 4 次数据拷贝（磁盘->内核缓冲区->用户缓冲区->Socket缓冲区->网卡）。零拷贝旨在减少这些开销。
      ```text
      传统模式数据流向示意图：
      [磁盘] --(1.DMA拷贝)--> [内核缓冲区] -------(2.CPU拷贝)------->
                                                                  |
                                                               [用户缓冲区]
                                                                  |
                                                                  v
      [网卡] <--(4.DMA拷贝)-- [Socket缓冲区] <-----(3.CPU拷贝)------+
      ```
    - **mmap (Memory Mapped Files)**
      - 原理：将磁盘文件映射到用户态的虚拟内存中，用户态可以直接操作内存来读写文件，省去了内核态到用户态的数据拷贝。
      - **RocketMQ**：广泛使用 mmap。它的 CommitLog（存储消息的主文件）默认大小 1GB，正是为了适应 mmap 的限制（Java 中 MappedByteBuffer 受限于 Integer.MAX_VALUE，且过大内存映射会影响性能）。RocketMQ 通过 mmap 提高写入和读取性能。
      - **Kafka**：主要用于索引文件（Index）的读写。
    - **sendfile**
      - 原理：Linux 系统调用。数据直接从磁盘读取到内核缓冲区（Page Cache），然后将描述符（位置、长度）传递给 Socket 缓冲区，数据通过 DMA 直接从内核缓冲区拷贝到网卡。全程数据不经过用户态。
      - **Kafka**：这是 Kafka 高吞吐量的核心武器之一。当消费者拉取消息时，Kafka 调用 `sendfile` (Java 中对应 `FileChannel.transferTo`) 直接将磁盘上的日志文件数据发送到网络，极大降低了 CPU 负载。
    - **总结**：RocketMQ 偏向于 mmap，因为它的存储结构涉及较多的随机读写；Kafka 在消费端重度依赖 sendfile，因为它主要进行顺序读取。
21. Push 模式 vs Pull 模式
    - **Push (推模式)**
      - **代表**：RabbitMQ, ActiveMQ
      - **原理**：由 Broker 主动将消息推送给已注册的消费者。实时性非常好，一旦有消息，会立刻送达。
      - **缺点**：如果生产者发送消息的速度远大于消费者的处理速度，很容易导致消费者被大量消息压垮。消费者需要实现流控机制（如 RabbitMQ 的 `prefetch_count`）来保护自己。
    - **Pull (拉模式)**
      - **代表**：Kafka, RocketMQ
      - **原理**：由 Consumer 主动向 Broker 发起请求，拉取消息。消费的主动权在消费者手中，可以根据自己的处理能力来决定一次拉取多少消息。
      - **缺点**：如果 Broker 没有消息，消费者的拉取请求可能是无效的，造成资源浪费。为了解决这个问题，通常会采用“长轮询（Long Polling）”机制：消费者发起拉取请求后，如果当前没有消息，Broker 会将请求挂起一段时间，直到有新消息到达或超时，从而降低了无效轮询的频率，兼顾了实时性和效率。
22. Azure Service Bus
    - **核心定位**：Azure 提供的完全托管的企业级消息中间件（PaaS）。支持 AMQP 1.0、HTTP/REST 协议，Premium（高级层）支持 Java JMS 2.0 标准。适用于高价值业务消息（如订单、支付）。
    - **核心模型**：
      - **Queues (队列)**：点对点模型 (Point-to-Point)。提供负载均衡，消费者竞争消费。
      - **Topics & Subscriptions (主题与订阅)**：发布/订阅模型 (Pub/Sub)。Topic 接收消息，Subscription 像虚拟队列一样接收副本。
      - **Filters (过滤器)**：Subscription 可以定义规则（支持 SQL 语法），只接收符合条件的消息。这比 RabbitMQ 的 Routing Key 更强大和灵活。
    - **如何保证顺序 (Message Sessions)**：
      - 普通队列在多消费者并发下无法严格保证全局 FIFO。
      - **Message Sessions (会话)**：通过指定 `SessionId`，Service Bus 确保具有相同 SessionId 的消息被“锁定”给同一个消费者实例，并按顺序处理。这是 Azure Service Bus 实现有序消费的核心机制。
    - **消息确认机制 (Peek-Lock vs Receive-and-Delete)**：
      - **Peek-Lock (默认/推荐)**：消费者读取消息后，消息在服务端被“锁定”变为不可见，但未删除。消费者处理成功后调用 `Complete()`；如果处理失败或超时，锁自动释放，消息重新可见（At-Least-Once 语义）。
      - **Receive-and-Delete**：消息发送给消费者后立即从队列删除。如果消费者崩溃，消息丢失（At-Most-Once 语义）。
    - **死信队列 (DLQ)**：
      - 每个 Queue 和 Subscription 默认都有一个 DLQ。
      - **进入 DLQ 的原因**：超过最大投递次数 (MaxDeliveryCount)、TTL 过期（需配置）、过滤器评估异常等。
    - **延迟消息**：
      - 原生支持。发送消息时设置 `ScheduledEnqueueTimeUtc`，消息会暂存在服务端，直到指定时间才对消费者可见。
    - **与 Azure Event Hubs (类 Kafka) 的区别**：
      - **Service Bus**：侧重于“消息 (Message)”。处理高价值、事务性、复杂路由的业务逻辑。
      - **Event Hubs**：侧重于“事件流 (Event Stream)”。类似于 Kafka，侧重高吞吐量、大数据摄入、分区日志架构。
23. Azure Service Bus相比RabbitMQ的优势
    - **总结**：Azure Service Bus 作为云原生 PaaS 服务，提供了许多开箱即用的企业级功能，而 RabbitMQ 作为一个开源自托管的 Broker，虽然灵活但需要更多的手动配置、插件和运维投入来实现类似的功能。
    - **Duplicate Detection (内置重复数据删除)**：
      - **Service Bus**: 提供开箱即用的服务器端重复数据删除功能。你可以在创建队列或主题时启用此功能，并设置一个时间窗口（如 10 分钟）。Service Bus 会在此期间记录所有消息的 `MessageId`，并自动丢弃任何重复的消息。这极大地简化了客户端的幂等性实现。
      - **RabbitMQ**: 核心功能不提供服务器端重复数据删除。幂等性保证完全是消费者的责任，通常需要借助外部存储（如 Redis）来跟踪已处理的消息 ID。虽然 `Stream` 插件提供了类似功能，但这并非其标准队列的特性。
    - **Scheduled Messages (计划消息)**：
      - **Service Bus**: 原生支持按消息设置精确的延迟投递时间。发送时只需设置 `ScheduledEnqueueTimeUtc` 属性，消息就会在服务端暂存，直到指定时间才对消费者可见。此功能精确、简单且对性能影响小。
      - **RabbitMQ**: 没有原生支持。主要通过 `rabbitmq-delayed-message-exchange` 插件实现，但该插件存在性能瓶颈和数据丢失的风险（节点故障时，未持久化的延迟消息会丢失），不推荐用于高并发或高可靠性场景。另一个方案是 TTL+DLX，但它无法实现精确的、任意时间的延迟，且存在队头阻塞问题。
    - **Transactions (事务)**：
      - **Service Bus**: 支持跨多个实体（队列/主题）的原子操作。一个强大的功能是，消费者可以在一个事务内接收一条消息，并发送多条消息到其他队列/主题，整个操作要么全部成功，要么全部失败。这为实现可靠的“处理-转发”模式提供了坚实基础。
      - **RabbitMQ**: 提供了 `txSelect`, `txCommit`, `txRollback` 事务机制，但它性能开销巨大，且仅限于生产者与 Broker 之间的交互，无法覆盖“接收并发送”的原子场景，因此在实践中很少被使用。更常见的是使用轻量级的 `Publisher Confirms` 机制来确保发送成功。
    - **Geo-Replication (异地复制)**：
      - **Service Bus**: 在高级版（Premium Tier）中提供开箱即用的异地灾备功能。你可以配对两个不同地理区域的命名空间，服务会自动同步元数据。在主区域发生灾难时，可以手动或自动故障转移到辅助区域，实现业务连续性，而这一切都由 Azure 平台管理。
      - **RabbitMQ**: 作为自托管服务，实现异地复制非常复杂。通常需要结合 `Federation` 或 `Shovel` 插件在不同数据中心的集群间同步消息。这需要自己管理网络、安全、数据一致性和故障转移逻辑，运维成本和复杂度远高于 Service Bus 的托管方案。