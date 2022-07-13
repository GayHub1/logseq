- tags:  #[[docker]] #[[RabbitMQ]] 
  ---
- # 启动单节点Rabbit MQ
	- ```sh
	  docker run -d --rm --name rabbitmq -p 5672:5672 -p 15672:15672 rabbitmq:3-management
	  ```
- # 使用 Docker Compose 启动3个 RabbitMQ 节点
	- ```sh
	  vi docker-compose.yml	
	  ```
	- ```yml
	  version: "2.0"
	  services:
	    rabbit1:
	      image: rabbitmq:3-management
	      hostname: rabbit1
	      ports:
	        - 5672:5672 #集群内部访问的端口
	        - 15672:15672 #外部访问的端口
	      environment:
	        - RABBITMQ_DEFAULT_USER=guest #用户名
	        - RABBITMQ_DEFAULT_PASS=guest #密码
	        - RABBITMQ_ERLANG_COOKIE='imoocrabbitmq'
	  
	    rabbit2:
	      image: rabbitmq:3-management
	      hostname: rabbit2
	      ports:
	        - 5673:5672
	      environment:
	        - RABBITMQ_ERLANG_COOKIE='imoocrabbitmq'
	      links:
	        - rabbit1
	  
	    rabbit3:
	      image: rabbitmq:3-management
	      hostname: rabbit3
	      ports:
	        - 5674:5672
	      environment:
	        - RABBITMQ_ERLANG_COOKIE='imoocrabbitmq'
	      links:
	        - rabbit1
	        - rabbit2
	  ```
- # 将3个 RabbitMQ 节点搭建为集群
  id:: 62c1573c-6d96-48e8-8a3e-1e668d9b3838
	- 启动docker-compose，按照脚本启动集群
	  ```sh
	  docker-compose up -d
	  ```
	  
	  进入2号节点
	  ```sh
	  docker exec -it root_rabbit2_1 bash 
	  ```
	  
	  停止2号节点的rabbitmq
	  ```sh
	  rabbitmqctl stop_app
	  ```
	  
	  配置2号节点，加入集群
	  ```sh
	  rabbitmqctl join_cluster rabbit@rabbit1
	  ```
	  
	  启动2号节点的rabbitmq
	  ```sh
	  rabbitmqctl start_app 
	  ```
	  
	  进入3号节点
	  ```sh
	  docker exec -it root_rabbit3_1 bash 
	  ```
	  
	  停止3号节点的rabbitmq
	  ```sh
	  rabbitmqctl stop_app
	  ```
	  
	  配置3号节点，加入集群
	  ```sh
	  rabbitmqctl join_cluster rabbit@rabbit1
	  ```
	  
	  启动3号节点的rabbitmq
	  ```sh
	  rabbitmqctl start_app 
	  ```