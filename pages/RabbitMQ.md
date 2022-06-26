- 基于 [[Erlang]] 的跨平台消息队列，天然具有高性能，使用[[AMQP]]协议提供客户端接口
- RabbitMQ安装
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
- 业务开发建议
	- 一个业务对应一个exchange
	- 将创建交换机/队列的操作固化在应用代码中，免去复杂的
	  运维操作，高效且不易出错
	- 一般来说，交换机由双方同时声明，队列由接收方声明并配
	  置绑定关系
	- 交换机/队列的参数一定要由双方开发团队确认，否则重复
	  声明时，若参数不一致，会导致声明失败
-