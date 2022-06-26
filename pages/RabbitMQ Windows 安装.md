- 安装 [Erlang OPT](https://www.erlang.org/downloads)
- 安装 [RabbitMQ](https://www.rabbitmq.com/install-windows.html)
- 开启网页管理端页面
	- ```sh
	  # 启用前端插件
	  rabbitmq-plugins enable rabbitmq_management
	  # 重启
	  rabbitmq-server restart
	  ```
	- 访问 [127.0.0.1:15672](http://127.0.0.1:15672/)
	- 默认用户名密码均为 **guest**