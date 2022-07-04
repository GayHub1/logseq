- 简介
  collapsed:: true
	- HAProxy是一款提供高可用性、负载均衡以及基于TCP(四层)和HTTP(七层)应用的代理软件
	- HAProxy适用于那些负载较大的web站点
	- HAProxy可以支持数以万计的并发连接
- 负载均衡 RabbitMQ
  collapsed:: true
	- 安装haproxy
	  collapsed:: true
		- ```sh
		  yum install haproxy -y
		  ```
	- 编辑hosts，使得haproxy能够通过主机名访问集群节点
	  collapsed:: true
		- ```sh
		  vi /etc/hosts
		  ```
	- 编辑haproxy配置文件
		- ```cfg
		  global
		      # 日志输出配置、所有日志都记录在本机，通过 local0 进行输出
		      log 127.0.0.1 local0 info
		      # 最大连接数
		      maxconn 4096
		      daemon
		  # 默认配置
		  defaults
		      # 应用全局的日志配置
		      log global
		      # 使用4层代理模式，7层代理模式则为"http"
		      mode tcp
		      # 日志类别
		      option tcplog
		      # 不记录健康检查的日志信息
		      option dontlognull
		      # 3次失败则认为服务不可用
		      retries 3
		      # 每个进程可用的最大连接数
		      maxconn 2000
		      # 连接超时
		      timeout connect 5s
		      # 客户端超时
		      timeout client 120s
		      # 服务端超时
		      timeout server 120s
		  # 绑定配置
		  listen rabbitmq_cluster
		      bind :5672
		      # 配置TCP模式
		      mode tcp
		      # 采用加权轮询的机制进行负载均衡
		      balance roundrobin
		      # RabbitMQ 集群节点配置 5秒检查一次 2次检查好的就是好的 3次坏的就是down了 轮询权重是 1
		      server mq01 mq01:5672 check inter 5000 rise 2 fall 3 weight 1
		      server mq02 mq02:5672 check inter 5000 rise 2 fall 3 weight 1
		      server mq03 mq03:5672 check inter 5000 rise 2 fall 3 weight 1
		  
		  # 配置监控页面
		  listen monitor
		      bind *:8100
		      mode http
		      option httplog
		      stats enable
		      stats uri /rabbitmq
		      stats refresh 5s
		  
		  
		  ```
	- 设置seLinux
	  collapsed:: true
		- ```sh
		  sudo setsebool -P haproxy_connect_any=1
		  ```
	- 关闭防火墙
	  collapsed:: true
		- ```sh
		  systemctl stop firewalld.service
		  systemctl disable firewalld.service
		  ```
	- 启动haproxy
	  collapsed:: true
		- ```sh
		  systemctl start haproxy
		  ```