1. ActiveMQ
2. RabbitMQ
   - RabbitMQ 中通过 Binding(绑定) 将 Exchange(交换器) 与 Queue(消息队列) 关联起来，在绑定的时候一般会指定一个 BindingKey(绑定建) ,这样 RabbitMQ 就知道如何正确将消息路由到队列了
   - 生产者将消息发送给交换器时，需要一个 RoutingKey,当 BindingKey 和 RoutingKey 相匹配时，消息会被路由到对应的队列中。
3. RabbitMQ 核心概念
   - 在 RabbitMQ 中，消息并不是直接被投递到 Queue(消息队列) 中的，中间还必须经过 Exchange(交换器) 这一层，Exchange(交换器) 会把我们的消息分配到对应的 Queue(消息队列) 中。
   - 生产者将消息发给交换器的时候，一般会指定一个 RoutingKey(路由键)，用来指定这个消息的路由规则，而这个 RoutingKey 需要与交换器类型和绑定键(BindingKey)联合使用才能最终生效。
4. 如何保证 RabbitMQ 高可用的
    - 镜像集群模式：每个 RabbitMQ 节点都有这个 queue 的一个完整镜像，包含 queue 的全部数据的意思。然后每次你写消息到 queue 的时候，都会自动把消息同步到多个实例的 queue 上
5. Kafka 如何保证消息的消费顺序
   - 每次添加消息到 Partition(分区) 的时候都会采用尾加法，如上图所示。 Kafka 只能为我们保证 Partition(分区) 中的消息有序。发送消息的时候指定 key/Partition，这样同一个 key 的消息可以保证只发送到同一个 partition，这个我们可以采用表/对象的 id 来作为 key 。
6. Kafka 如何保证消息不丢失
   - 消息在被追加到 Partition(分区)的时候都会分配一个特定的偏移量（offset）。偏移量（offset)表示 Consumer 当前消费到的 Partition(分区)的所在的位置。手动关闭自动提交 offset，每次在真正消费完消息之后再自己手动提交 offset 。
7. kafka 出现消息重复消费的原因
   - 服务端侧已经消费的数据没有成功提交 offset（根本原因），比如你刚刚消费完消息之后，还没提交 offset，结果自己挂掉了，那么这个消息理论上就会被消费两次
8. Kafka 如何保证消息不重复消费
   - 消费消息服务做幂等校验，比如 Redis 的 set、MySQL 的主键等天然的幂等功能。这种方法最有效
9. 什么是死信队列？如何导致的
   - 当消息在一个队列中变成死信 (dead message) 之后，它能被重新发送到另一个交换器中，这个交换器就是 DLX，绑定 DLX 的队列就称之为死信队列。（消息被拒、消息 TTL 过期、队列满了）
10. RocketMQ
11. Kafka：Topic（主题） : Producer 将消息发送到特定的主题，Consumer 通过订阅特定的 Topic(主题) 来消费消息。Partition（分区） : Partition 属于 Topic 的一部分。一个 Topic 可以有多个 Partition ，并且同一 Topic 下的 Partition 可以分布在不同的 Broker 上，这也就表明一个 Topic 可以横跨多个 Broker。
12. 几种消息队列的对比
    - ActiveMQ 的社区算是比较成熟，但是较目前来说，ActiveMQ 的性能比较差，而且版本迭代很慢，不推荐使用，已经被淘汰了。
    - RabbitMQ 在吞吐量方面虽然稍逊于 Kafka、RocketMQ 和 Pulsar，但是由于它基于 Erlang 开发，所以并发能力很强，性能极其好，延时很低，达到微秒级。但是也因为 RabbitMQ 基于 Erlang 开发，所以国内很少有公司有实力做 Erlang 源码级别的研究和定制。如果业务场景对并发量要求不是太高（十万级、百万级），那这几种消息队列中，RabbitMQ 或许是你的首选。
    - RocketMQ 和 Pulsar 支持强一致性，对消息一致性要求比较高的场景可以使用。RocketMQ 阿里出品，Java 系开源项目，源代码我们可以直接阅读，然后可以定制自己公司的 MQ，并且 RocketMQ 有阿里巴巴的实际业务场景的实战考验。
    - Kafka 的特点其实很明显，就是仅仅提供较少的核心功能，但是提供超高的吞吐量，ms 级的延迟，极高的可用性以及可靠性，而且分布式可以任意扩展。极致的性能：基于 Scala 和 Java 语言开发，设计中大量使用了批量处理和异步的思想，最高可以每秒处理千万级别的消息。同时 Kafka 最好是支撑较少的 topic 数量即可，保证其超高吞吐量。Kafka 唯一的一点劣势是有可能消息重复消费，那么对数据准确性会造成极其轻微的影响，在大数据领域中以及日志采集中，这点轻微影响可以忽略这个特性天然适合大数据实时计算以及日志收集。如果是大数据领域的实时计算、日志采集等场景，用 Kafka 是业内标准的，绝对没问题，社区活跃度很高，绝对不会黄，何况几乎是全世界这个领域的事实性规范。
13. RabbitMQ 的事务机制局限于生产者和 RabbitMQ 之间，无法覆盖到消费者，也没有 Kafka 和 RocketMQ 中的全局事务支持
14. 在 Kafka 或 RocketMQ 中，使用事务消息模式时，通常是生产者的本地事务与消息发送的原子性，而不是与消费者的本地事务同时绑定。可以使用Kafka+saga模式确保在消费者的本地事务失败时通过补偿机制回滚前面的操作。


