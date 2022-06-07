- #[[excerpt]] [深入理解RocketMQ延迟消息 - 云+社区 - 腾讯云](https://cloud.tencent.com/developer/article/1581368)
- tags: #[[SimpRead]]
- read date: [[2022_06_07  ]]
- desc: 延迟消息是实际开发中一个非常有用的功能，本文第一部分从整体上介绍秒级精度延迟消息的实现思路，在第二部分结合RocketMQ的延迟消息实现，进行细致的讲解，点出关...
- [📌](<http://localhost:7026/reading/7?title=深入理解RocketMQ延迟消息 - 云+社区 - 腾讯云#id=1654564191307>)  基本概念：延迟消息是指生产者发送消息发送消息后，不能立刻被消费者消费，需要等待指定的时间后才可以被消费。
-
-
- [📌](<http://localhost:7026/reading/7?title=深入理解RocketMQ延迟消息 - 云+社区 - 腾讯云#id=1654564199168>)  延迟消息
- [📌](<http://localhost:7026/reading/7?title=深入理解RocketMQ延迟消息 - 云+社区 - 腾讯云#id=1654564184432>)  一些消息[中间件](https://cloud.tencent.com/product/tdmq?from=10680)的 Broker 端内置了延迟消息支持的能力
	- [📌](<http://localhost:7026/reading/7?title=深入理解RocketMQ延迟消息 - 云+社区 - 腾讯云#id=1654564179730>)  **NSQ：**这是一个 go 语言的消息中间件，其通过内存中的优先级队列来保存延迟消息，支持秒级精度
	- [📌](<http://localhost:7026/reading/7?title=深入理解RocketMQ延迟消息 - 云+社区 - 腾讯云#id=1654564231604>)  **QMQ：**采用双重时间轮实现。
	- [📌](<http://localhost:7026/reading/7?title=深入理解RocketMQ延迟消息 - 云+社区 - 腾讯云#id=1654564246738>)  **RabbitMQ：**需要安装一个 rabbitmq_delayed_message_exchange 插件。
	  id:: 4cecb7da-45d3-467b-99b4-d8fc26f97e12
	- [📌](<http://localhost:7026/reading/7?title=深入理解RocketMQ延迟消息 - 云+社区 - 腾讯云#id=1654564251474>)  **RocketMQ：**RocketMQ 开源版本延迟消息临时存储在一个内部主题中，
- [📌](<http://localhost:7026/reading/7?title=深入理解RocketMQ延迟消息 - 云+社区 - 腾讯云#id=1654564358985>)  一些消息中间件原生并不支持延迟消息，如 Kafka。在这种情况下，可以选择对 Kafka 进行改造，但是成本较大。另外一种方式是使用第三方临时存储，并加一层代理。
- [📌](<http://localhost:7026/reading/7?title=深入理解RocketMQ延迟消息 - 云+社区 - 腾讯云#id=1654564386796>)  第三方存储选型要求：
	- [📌](<http://localhost:7026/reading/7?title=深入理解RocketMQ延迟消息 - 云+社区 - 腾讯云#id=1654564390232>)  高性能：
	- [📌](<http://localhost:7026/reading/7?title=深入理解RocketMQ延迟消息 - 云+社区 - 腾讯云#id=1654564409786>)  **支持排序：**支持按照某个字段对消息进行排序，对于延迟消息需要按照时间进行排序。
	- [📌](<http://localhost:7026/reading/7?title=深入理解RocketMQ延迟消息 - 云+社区 - 腾讯云#id=1654564431338>)  **支持长时间保存：**一些业务的延迟消息，需要延迟几个月，甚至更长，所以延迟消息必须能长时间保留。
	- [📌](<http://localhost:7026/reading/7?title=深入理解RocketMQ延迟消息 - 云+社区 - 腾讯云#id=1654564442978>)  **高可靠：**延迟消息写入后，不能丢失，需要进行持久化，并进行备份
-
- DDMQ
	- ![](https://ask.qcloudimg.com/http-save/yehe-5457352/bv64pwxfcb.jpeg)
	- [📌](<http://localhost:7026/reading/7?title=深入理解RocketMQ延迟消息 - 云+社区 - 腾讯云#id=1654564838304>)  这种方式的好处是，因为 delay service 的延迟投递能力是独立于 broker 实现的，不需要对 broker 做任何改造，对于任意 MQ 类型都可以提供支持延迟消息的能力。
	- [📌](<http://localhost:7026/reading/7?title=深入理解RocketMQ延迟消息 - 云+社区 - 腾讯云#id=1654564347706>)  显然，临时存储模块和延迟服务模块，是延迟消息实现的关键。
	- [📌](<http://localhost:7026/reading/7?title=深入理解RocketMQ延迟消息 - 云+社区 - 腾讯云#id=1654564925637>)  为了保证服务的高可用
	- [📌](<http://localhost:7026/reading/7?title=深入理解RocketMQ延迟消息 - 云+社区 - 腾讯云#id=1654564928550>)  为了保证数据不丢失
	- [📌](<http://localhost:7026/reading/7?title=深入理解RocketMQ延迟消息 - 云+社区 - 腾讯云#id=1654564980463>)  master 要记录自己当前投递到的时间到一个共享存储中
	- [📌](<http://localhost:7026/reading/7?title=深入理解RocketMQ延迟消息 - 云+社区 - 腾讯云#id=1654564985823>)  为了避免重复投递，delay service 需要进行选主  ，可以借助于 zookeeper、etcd 等实现。
-
- RocketMQ
	- [📌](<http://localhost:7026/reading/7?title=深入理解RocketMQ延迟消息 - 云+社区 - 腾讯云#id=1654565024047>)  开源 RocketMQ 支持延迟消息，但是不支持秒级精度。默认支持 18 个 level 的延迟消息，这是通过 broker 端的 messageDelayLevel 配置项确定的
	- [📌](<http://localhost:7026/reading/7?title=深入理解RocketMQ延迟消息 - 云+社区 - 腾讯云#id=1654565051741>)  延迟级别的值可以进行修改，以满足自己的业务需求，可以修改 / 添加新的 level。例如：你想支持 2 天的延迟，修改最后一个 level 的值为 2d，这个时候依然是 18 个 level；也可以增加一个 2d，这个时候总共就有 19 个 level。
	- [📌](<http://localhost:7026/reading/7?title=深入理解RocketMQ延迟消息 - 云+社区 - 腾讯云#id=1654565132382>)  ![](https://ask.qcloudimg.com/http-save/yehe-5457352/iuvhrfhpff.jpeg)
	- [📌](<http://localhost:7026/reading/7?title=深入理解RocketMQ延迟消息 - 云+社区 - 腾讯云#id=1654565141805>)  可以看到，总共有 6 个步骤，下面会对这 6 个步骤进行详细的讲解：
		- collapsed:: true
		  1.  修改消息 Topic 名称和队列信息
			- [📌](<http://localhost:7026/reading/7?title=深入理解RocketMQ延迟消息 - 云+社区 - 腾讯云#id=1654565311792>)  由于消息一旦存储到 ConsumeQueue 中，消费者就能消费到，而延迟消息不能被立即消费，所以这里将 Topic 的名称修改为 SCHEDULE_TOPIC_XXXX，并根据延迟级别确定要投递到哪个队列下。
			  [📌](<http://localhost:7026/reading/7?title=深入理解RocketMQ延迟消息 - 云+社区 - 腾讯云#id=1654565998386>)  org.apache.rocketmq.store.CommitLog#putMessage
			  ![](https://ask.qcloudimg.com/http-save/yehe-5457352/j4qjv0o9lm.jpeg)
		- collapsed:: true
		  2.  转发消息到延迟主题的 CosumeQueue 中
			- [📌](<http://localhost:7026/reading/7?title=深入理解RocketMQ延迟消息 - 云+社区 - 腾讯云#id=1654566025347>)  CommitLog 中的消息转发到 CosumeQueue 中是异步进行的。在转发过程中，会对延迟消息进行特殊处理，主要是计算这条延迟消息需要在什么时候进行投递
			  [📌](<http://localhost:7026/reading/7?title=深入理解RocketMQ延迟消息 - 云+社区 - 腾讯云#id=1654566088250>)  ```
			  投递时间=消息存储时间(storeTimestamp) + 延迟级别对应的时间
			  ```
			  [📌](<http://localhost:7026/reading/7?title=深入理解RocketMQ延迟消息 - 云+社区 - 腾讯云#id=1654566100973>)  *   **Commit Log Offset：**记录在 CommitLog 中的位置。
			  	* **Size：**记录消息的大小
			  	* **Message Tag HashCode：**记录消息 Tag 的哈希值，用于消息过滤。特别的，对于延迟消息，这个字段记录的是消息的投递时间戳。这也是为什么 java 中 hashCode 方法返回一个 int 型，只占用 4 个字节，而这里 Message Tag HashCode 字段却设计成 8 个字节的原因。
			  
			  [📌](<http://localhost:7026/reading/7?title=深入理解RocketMQ延迟消息 - 云+社区 - 腾讯云#id=1654566107270>)  需要注意的是，会将计算出的投递时间当做消息 Tag 的哈希值存储到 CosumeQueue 中，CosumeQueue 单个存储单元组成结构如下图所示：
			    
			  [📌](<http://localhost:7026/reading/7?title=深入理解RocketMQ延迟消息 - 云+社区 - 腾讯云#id=1654566108446>)  ![](https://ask.qcloudimg.com/http-save/yehe-5457352/7c2hsw38d0.jpeg)
			- Commit Log Offset：记录在CommitLog中的位置。
			  Size：记录消息的大小
			  Message Tag HashCode：记录消息Tag的哈希值，用于消息过滤。特别的，对于延迟消息，这个字段记录的是消息的投递时间戳。这也是为什么java中hashCode方法返回一个int型，只占用4个字节，而这里Message Tag HashCode字段却设计成8个字节的原因。
			  相关源码参见：
			- CommitLog#checkMessageAndReturnSize
			- ![](https://ask.qcloudimg.com/http-save/yehe-5457352/idt5jrib4n.jpeg)
		- collapsed:: true
		  3.  延迟服务消费 SCHEDULE_TOPIC_XXXX 消息
			- Broker内部有一个ScheduleMessageService类，其充当延迟服务，消费SCHEDULE_TOPIC_XXXX中的消息，并投递到目标Topic中。
			- ScheduleMessageService在启动时，其会创建一个定时器Timer，并根据延迟级别的个数，启动对应数量的TimerTask，每个TimerTask负责一个延迟级别的消费与投递。
			- 相关源码如下所示：
			- ScheduleMessageService#start
			- ![](https://ask.qcloudimg.com/http-save/yehe-5457352/5e2ja5uzu0.jpeg)
			- [📌](<http://localhost:7026/reading/7?title=深入理解RocketMQ延迟消息 - 云+社区 - 腾讯云#id=1654566524904>)  需要注意的是，每个 TimeTask 在检查消息是否到期时，首先检查对应队列中尚未投递第一条消息，如果这条消息没到期，那么之后的消息都不会检查。如果到期了，则进行投递，并检查之后的消息是否到期。
		- collapsed:: true
		  4.  将信息重新存储到 CommitLog 中
			- 在将消息到期后，需要投递到目标Topic。由于在第一步已经记录了原来的Topic和队列信息，因此这里重新设置，再存储到CommitLog即可。此外，由于之前Message Tag HashCode字段存储的是消息的投递时间，这里需要重新计算tag的哈希值后再存储。
			- 源码参见：DeliverDelayedMessageTimerTask的messageTimeup方法。
		- 5.  将消息投递到目标 Topic 中
		- collapsed:: true
		  6.  消费者消费目标 topic 中的数据
			- 这一步与第二步类似，不过由于消息的Topic名称已经改为了目标Topic。因此消息会直接投递到目标Topic的ConsumeQueue中，之后消费者即消费到这条消息。
	- [📌](<http://localhost:7026/reading/7?title=深入理解RocketMQ延迟消息 - 云+社区 - 腾讯云#id=1654566604144>)  消息重试的 16 个级别，实际上是把延迟消息 18 个级别的前两个 level 去掉了。