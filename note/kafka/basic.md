# Kafka 介绍

## 消息系统
定义: **消息系统负责将数据从一个应用传递到另一个应用, 应用只需关注数据而无需关注数据在两个应用间是如何传递的，分布式消息传递基于可靠
的消息队列在客户端应用和消息系统之间异步地传递消息。主要有两种模式**
- 点对点消息系统
**消息持久化到一个队列中, 此时将有一个或者多个消费者消费队列中的数据, 但是一条消息只能被消费一次。当有一个消费者消费掉队列中的某一个
消息之后, 该条消息则从消息队列中删除。此模式即使有多个消费者同时消费数据, 也可以保持数据的*顺序*。**
- 发布-订阅消息系统
**将消息持久化到一个topic中, 消费者可以订阅一个或多个topic, 消费者可以消费这个topic中的所有的数据, 同一条数据可以被多个消费者
消费, 数据被消费后不会立即被删除。此模式中消息的生产者称为发布者, 消息的消费者称为订阅者。**

## Kafka术语
- 什么是Kafka
Kafka是apache的一个分布式 发布-订阅模式消息系统, 可以支持海量数据的数据传递, 在离线和实时的消息处理中, Kafka都有广泛的应用。Kafka会将
消息持久化到磁盘中并进行备份保证数据的安全, 可以在数据高速处理的同时也保证了数据处理的低延迟和零丢失。

## 安装和使用

## 生产和消费
- Kafka producer消息分区  
    - key: 提供描述信息的额外信息; 用来决定消息写入那个分区, 所有具有相同Key的消息会被分配到同一个分区中。若不存在Key, 即key为null,使用
    默认的分区分配器->DefaultPartitioner, round-robin实现负载均衡。若存在key, 使用默认的分区分配器, 对Key进行hash确定消息应该分配到
    哪个分区(决定分区时使用的是全部分区数, 而不是可用分区数, 即如果某个分区不可用消息也可能被分配过去造成消息写入失败)。
    -自定义分区器: 实现Partitioner接口 重写partition方法
    
- Kafka consumer消费者组    
    - Kafka的消费者是消费者组的一部分, 当多个消费者形成一个消费者组来消费topic时, 每个消费者会受到不同分区的消息。
    如果topic中具有多个分区即partition, 并且消费者组里面只有一个消费者那么该topic中的所有partition的消息都会发送给唯一的一个consumer
    
    如果topic中具有 t 个分区, 消费者组具有 1 < c < t个消费者, 那么所有的消息会分别发送给消费者(存在消费分配和负载均衡等策略来具体消费消息)
    
    如果topic中具有 t 个分区, 消费者组具有 t 个消费者, 那么每个消费者会分别收到一个partition的消息。
    
    如果topic中具有 t 个分区, 消费者组具有 c > t 个消费者, 那么多余的消费者就会空闲不会收到消息。 
    
    - Kafka的一个特性, 只需要写入一次消息, 可以支持任意多个应用去消费消息。即每个应用都可以读取到全量的消息, 为达到每一个应用都可以读到
    全量消息的目的, 应用就需要有不同的消费者组, 多个不同的消费者组去消费同一个 topic。
- kafka 发送消息
    - 直接发送, 不关心发送结果
    - 发送完, 接收返回的Future对象的get()方法判断消息发送结果是否成功
    - 发送方法send, 提供回调函数。当接收到broker的返回结果后自动调用
        
- Kafka 消费消息
    - 自动提交位移
    - 手动提交当前位移
    - 手动异步提交当前位移
    - 手动异步提交当前位移带回调
    - 混合同步与异步提交位移