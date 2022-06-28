- 基于 [[Erlang]] 的跨平台消息队列，天然具有高性能，使用[[AMQP]]协议提供客户端接口
- RabbitMQ安装
  collapsed:: true
	- [[RabbitMQ Windows 安装]]
- 命令行工具使用
  collapsed:: true
	- 状态
	  collapsed:: true
		- 查看状态
			- ```sh
			  rabbitmqctl status
			  ```
		- 查看绑定
			- ```sh
			  rabbitmqctl list_bindings
			  ```
		- 查看channel
			- ```sh
			  rabbitmqctl list_channels
			  ```
		- 查看connection
			- ```sh
			  rabbitmqctl list_connections
			  ```
		- 查看 消费者
			- ```sh
			  rabbitmqctl list_consumers
			  ```
		- 查看交换机
			- ```sh
			  rabbitmqctl list_exchanges
			  ```
	- 队列
	  collapsed:: true
		- 查看队列
			- ```sh
			  rabbitmqctl list_queues
			  ```
		- 删除队列
			- ```sh
			  rabbitmqctl delete_queue
			  ```
		- 清空队列
			- ```sh
			  rabbitmqctl purge_queuq
			  ```
	- 用户
	  collapsed:: true
		- 新建用户
			- ```sh
			  rabbitmqctl add_user
			  ```
		- 修改用户密码
		- ```sh
		  rabbitmqctl change_password
		  ```
		- 删除用户
			- ```sh
			  rabbitmqctl delete_user
			  ```
		- 查看用户
			- ```sh
			  rabbitmqctl list_users
			  ```
		- 设置用户角色
			- ```sh
			  rabbitmqctl rabbitmqctl set_user_tags
			  ```
		-
	- 应用
	  collapsed:: true
		- 启动应用
			- ```sh
			  rabbitmqctl start_app
			  ```
		- 关闭应用,保留Erlang虚拟机（暂停）
			- ```sh
			  rabbitmqctl stop_app
			  ```
		- 关闭应用 并关闭Erlang虚拟机
			- ```sh
			  rabbitmqctl stop
			  ```
	- 集群
	  collapsed:: true
		- 加入集群
			- ```sh
			  rabbitmqctl join_cluster
			  ```
		- 离开集群
		  collapsed:: true
			- ```sh
			  rabbitmqctl reset
			  ```
	- 镜像队列
	  collapsed:: true
		- 设置镜像队列
			- ```sh
			  rabbitmqctl sync_queue
			  ```
		- 取消镜像队列
			- ```sh
			  rabbitmqctl cancel_sync_queue
			  ```
- 业务中使用
  collapsed:: true
	- spring-rabbit
	  collapsed:: true
		- 依赖
		  collapsed:: true
			- ```gradle
			  testImplementation 'org.springframework.amqp:spring-rabbit-test'
			  ```
		- 配置
		  collapsed:: true
			- ```yaml
			  rabbitmq.host=localhost
			  rabbitmq.port=5672
			  rabbitmq.username=guest
			  rabbitmq.password=guest
			  ```
		- 配置线程池
		  collapsed:: true
			- ```java
			  @Configuration
			  @EnableAsync
			  public class AsyncTaskConfig implements AsyncConfigurer {
			  
			      // ThredPoolTaskExcutor的处理流程
			      // 当池子大小小于corePoolSize，就新建线程，并处理请求
			      // 当池子大小等于corePoolSize，把请求放入workQueue中，池子里的空闲线程就去workQueue中取任务并处理
			      // 当workQueue放不下任务时，就新建线程入池，并处理请求，如果池子大小撑到了maximumPoolSize，就用RejectedExecutionHandler来做拒绝处理
			      // 当池子的线程数大于corePoolSize时，多余的线程会等待keepAliveTime长时间，如果无请求可处理就自行销毁
			  
			      @Override
			      @Bean
			      public Executor getAsyncExecutor() {
			          ThreadPoolTaskExecutor threadPool = new ThreadPoolTaskExecutor();
			          //设置核心线程数
			          threadPool.setCorePoolSize(10);
			          //设置最大线程数
			          threadPool.setMaxPoolSize(100);
			          //线程池所使用的缓冲队列
			          threadPool.setQueueCapacity(10);
			          //等待任务在关机时完成--表明等待所有线程执行完
			          threadPool.setWaitForTasksToCompleteOnShutdown(true);
			          // 等待时间 （默认为0，此时立即停止），并没等待xx秒后强制停止
			          threadPool.setAwaitTerminationSeconds(60);
			          //  线程名称前缀
			          threadPool.setThreadNamePrefix("Rabbit-Async-");
			          // 初始化线程
			          threadPool.initialize();
			          return threadPool;
			      }
			  
			      @Override
			      public AsyncUncaughtExceptionHandler getAsyncUncaughtExceptionHandler() {
			          return null;
			      }
			  }
			  ```
		- 创建队列
		  collapsed:: true
			- Direct
			  collapsed:: true
				- ```java
				              channel.exchangeDeclare(
				                      "exchange.order.restaurant",
				                      BuiltinExchangeType.DIRECT,
				                      true,
				                      false,
				                      null);
				  
				              channel.queueDeclare(
				                      "queue.order",
				                      true,
				                      false,
				                      false,
				                      null);
				  
				              channel.queueBind(
				                      "queue.order",
				                      "exchange.order.restaurant",
				                      "key.order");
				  ```
			- Fanout
				- ```java
				              channel.exchangeDeclare(
				                      "exchange.settlement.order",
				                      BuiltinExchangeType.FANOUT,
				                      true,
				                      false,
				                      null);
				  
				              channel.queueDeclare(
				                      "queue.order",
				                      true,
				                      false,
				                      false,
				                      null);
				  
				              channel.queueBind(
				                      "queue.order",
				                      "exchange.settlement.order",
				                      "key.order");
				  ```
			- Topic
				- ```java
				              channel.exchangeDeclare(
				                      "exchange.order.reward",
				                      BuiltinExchangeType.TOPIC,
				                      true,
				                      false,
				                      null);
				  
				              channel.queueDeclare(
				                      "queue.order",
				                      true,
				                      false,
				                      false,
				                      null);
				  
				              channel.queueBind(
				                      "queue.order",
				                      "exchange.order.reward",
				                      "key.order");
				  ```
		- 监听队列
		  collapsed:: true
			- ```java
			      ObjectMapper objectMapper = new ObjectMapper();
			  
			      DeliverCallback deliverCallback = (consumerTag, message) -> {
			          String messageBody = new String(message.getBody());
			          log.info("deliverCallback:messageBody:{}", messageBody);
			          ConnectionFactory connectionFactory = new ConnectionFactory();
			          connectionFactory.setHost("localhost");
			          try {
			              OrderMessageDTO orderMessageDTO = objectMapper.readValue(messageBody,
			                      OrderMessageDTO.class);
			              List<DeliverymanPO> deliverymanPOS = deliverymanDao.selectAvaliableDeliveryman(DeliverymanStatus.AVALIABIE);
			              orderMessageDTO.setDeliverymanId(deliverymanPOS.get(0).getId());
			              log.info("onMessage:restaurantOrderMessageDTO:{}", orderMessageDTO);
			  
			              try (Connection connection = connectionFactory.newConnection();
			                   Channel channel = connection.createChannel()) {
			                  String messageToSend = objectMapper.writeValueAsString(orderMessageDTO);
			                  channel.basicPublish("exchange.order.restaurant", "key.order", null, messageToSend.getBytes());
			              }
			          } catch (JsonProcessingException | TimeoutException e) {
			              e.printStackTrace();
			          }
			      };
			  
			  
			  
			  channel.basicConsume("queue.deliveryman", true, deliverCallback, consumerTag -> {
			              });
			  ```
		- 发送消息
		  collapsed:: true
			- ```java
			  try (Connection connection = connectionFactory.newConnection();
			                               Channel channel = connection.createChannel()) {
			                              String messageToSend = objectMapper.writeValueAsString(orderMessageDTO);
			                              channel.basicPublish("exchange.order.deliveryman", "key.deliveryman", null,
			                                      messageToSend.getBytes());
			                          }
			  ```
		- 启动监听
		  collapsed:: true
			- ```java
			  @Slf4j
			  @Configuration
			  public class RabbitConfig {
			  
			      @Autowired
			      OrderMessageService orderMessageService;
			  
			      @Autowired
			      public void startListenMessage() throws IOException, TimeoutException, InterruptedException {
			          orderMessageService.handleMessage();
			      }
			  }
			  ```
			- orderMessage
				- ```java
				  @Slf4j
				  @Service
				  public class OrderMessageService {
				  
				      @Value("${rabbitmq.exchange}")
				      public String exchangeName;
				      @Value("${rabbitmq.deliveryman-routing-key}")
				      public String deliverymanRoutingKey;
				      @Value("${rabbitmq.settlement-routing-key}")
				      public String settlementRoutingKey;
				      @Value("${rabbitmq.reward-routing-key}")
				      public String rewardRoutingKey;
				  
				  
				      @Autowired
				      private OrderDetailDao orderDetailDao;
				      ObjectMapper objectMapper = new ObjectMapper();
				  
				  
				      @Async
				      public void handleMessage() throws IOException, TimeoutException, InterruptedException {
				          log.info("start linstening message");
				          ConnectionFactory connectionFactory = new ConnectionFactory();
				          connectionFactory.setHost("localhost");
				          connectionFactory.setHost("localhost");
				          try (Connection connection = connectionFactory.newConnection();
				               Channel channel = connection.createChannel()) {
				  		........
				            } 
				      }
				  }
				            
				  ```
	- springboot-rabbit
- 业务开发建议
  collapsed:: true
	- 一个业务对应一个exchange
	- 将创建交换机/队列的操作固化在应用代码中，免去复杂的
	  运维操作，高效且不易出错
	- 一般来说，交换机由双方同时声明，队列由接收方声明并配
	  置绑定关系
	- 交换机/队列的参数一定要由双方开发团队确认，否则重复
	  声明时，若参数不一致，会导致声明失败
-