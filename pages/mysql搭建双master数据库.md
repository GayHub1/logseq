- ![image.png](../assets/image_1654652170641_0.png)
- ## docker-compose
	- 目录结构
		- ![image.png](../assets/image_1654652794942_0.png)
	- master-1文件夹
	  collapsed:: true
		- Dockerfile  -- docker 镜像打包文件
			- ```Dockerfile  
			  FROM mysql:5.7.17
			  MAINTAINER harrison
			  ADD ./master-1/my.cnf /etc/mysql/my.cnf
			  ```
		- my.cnf master1的mysql配置文件
			- ```cnf
			  [mysqld]
			  ## 设置server_id，一般设置为IP，注意要唯一
			  server_id=1
			  ## 复制过滤：也就是指定哪个数据库不用同步（mysql库一般不同步）
			  binlog-ignore-db=mysql
			  ## 开启二进制日志功能，可以随便取，最好有含义（关键就是这里了）
			  log-bin=zhuoke-bin
			  # 设置一个 binlog 文件的最大字节
			  # 设置最大 100MB
			  max_binlog_size=104857600
			  ## 为每个session分配的内存，在事务过程中用来存储二进制日志的缓存
			  binlog_cache_size=1M
			  ## 主从复制的格式（mixed,statement,row，默认格式是statement）
			  binlog_format=ROW
			  ## 二进制日志自动删除/过期的天数。默认值为0，表示不自动删除。
			  expire_logs_days=7
			  ## 跳过主从复制中遇到的所有错误或指定类型的错误，避免slave端复制中断。
			  ## 如：1062错误是指一些主键重复，1032错误是因为主从数据库数据不一致
			  slave_skip_errors=1062
			  ```
	- master-2文件夹
	  collapsed:: true
		- 结构与master-1一致 ，不同在于Dockerfile  中目录改为master-2，my.cnf 中**server_id**与master-1不能一样
		- Dockerfile  -- docker 镜像打包文件
			- ```Dockerfile  
			  FROM mysql:5.7.17
			  MAINTAINER harrison
			  ADD ./master-2/my.cnf /etc/mysql/my.cnf
			  ```
		- my.cnf master1的mysql配置文件
			- ```cnf
			  [mysqld]
			  ## 设置server_id，一般设置为IP，注意要唯一
			  server_id=2
			  ## 复制过滤：也就是指定哪个数据库不用同步（mysql库一般不同步）
			  binlog-ignore-db=mysql
			  ## 开启二进制日志功能，可以随便取，最好有含义（关键就是这里了）
			  log-bin=zhuoke-bin
			  # 设置一个 binlog 文件的最大字节
			  # 设置最大 100MB
			  max_binlog_size=104857600
			  ## 为每个session分配的内存，在事务过程中用来存储二进制日志的缓存
			  binlog_cache_size=1M
			  ## 主从复制的格式（mixed,statement,row，默认格式是statement）
			  binlog_format=ROW
			  ## 二进制日志自动删除/过期的天数。默认值为0，表示不自动删除。
			  expire_logs_days=7
			  ## 跳过主从复制中遇到的所有错误或指定类型的错误，避免slave端复制中断。
			  ## 如：1062错误是指一些主键重复，1032错误是因为主从数据库数据不一致
			  slave_skip_errors=1062
			  ```
	- docker-compose.yaml
		- ```Yaml
		  
		  ```