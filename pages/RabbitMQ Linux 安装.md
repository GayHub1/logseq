- # 单节点安装Rabbitmq指南
	- 设置主机名
	  collapsed:: true
		- 设置主机名称，注意将星号替换为数字
		- ```sh
		  hostnamectl set-hostname mq0*.localdomain
		  ```
		- 在hosts文件中，前两行里加入主机名称
		- ```sh
		  vi /etc/hosts
		  ```
	- 安装系统依赖包
	  collapsed:: true
		- 安装epel
		- ```sh
		  sudo yum install epel-release -y
		  ```
		- 安装erlang
		- ```sh
		  sudo yum install erlang -y
		  ```
		- 安装socat
		- ```sh
		  yum install socat -y
		  ```
		- 安装wget
		- ```sh
		  yum install wget -y
		  ```
	- 安装rabbit
		- 下载rabbitmq安装包
		- ```sh
		  wget https://github.com/rabbitmq/rabbitmq-server/releases/download/v3.8.8/rabbitmq-server-3.8.8-1.el8.noarch.rpm
		  ```
		- 导入rabbitmq密钥
		- ```sh
		  rpm -import https://www.rabbitmq.com/rabbitmq-release-signing-key.asc
		  ```
		- 安装rabbitmq
		- ```sh
		  rpm -ivh rabbitmq-server-3.8.8-1.el8.noarch.rpm
		  ```
		- 启动rabbitmq
		- ```sh
		  systemctl start rabbitmq-server
		  ```
	- 查看rabbitmq服务状态
		- ```sh
		  systemctl status rabbitmq-server
		  ```
	- 启用管控台插件
	  collapsed:: true
		- ```sh
		  rabbitmq-plugins enable rabbitmq_management
		  ```
	- 关闭系统防火墙
	  collapsed:: true
		- ```sh
		  systemctl stop firewalld.service
		  systemctl disable firewalld.service
		  ```
	- 添加测试账户
		- ```sh
		  rabbitmqctl add_user test test
		  rabbitmqctl set_user_tags test administrator
		  rabbitmqctl set_permissions -p / test ".*" ".*" ".*"
		  ```
-
- # RabbitMQ集群配置指南
- 在集群所有节点安装rabbitmq
- 编辑hosts,使得节点间可以通过主机名互相访问
  collapsed:: true
	- ```sh
	  vi /etc/hosts
	  ```
	- ![Replaced by Image Uploder](https://cdn.jsdelivr.net/gh/GayHub1/images@master/img/image-20220703005955976_1656782255264_0.png)
- 修改.erlang.cookie权限
  collapsed:: true
	- ```sh
	  chmod 777 /var/lib/rabbitmq/.erlang.cookie
	  ```
- 将主节点的.erlang.cookie文件传输至集群所有节点
  collapsed:: true
	- ```sh
	  scp /var/lib/rabbitmq/.erlang.cookie root@mq02:/var/lib/rabbitmq
	  ```
- 复原.erlang.cookie权限
  collapsed:: true
	- `````sh
	  chmod 400 /var/lib/rabbitmq/.erlang.cookie
	  ```
- 加入集群
  collapsed:: true
	- ```sh
	  rabbitmqctl stop_app
	  rabbitmqctl join_cluster --ram rabbit@mq01
	  rabbitmqctl start_app
	  ```