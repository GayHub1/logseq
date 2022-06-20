- ### Windows安装
  在Window下的安装较为简单，在[Windows](https://github.com/microsoftarchive/redis/releases) 页面选择最新版本下载。安装即可 ()
- ### Linux安装 
  Linux 可以通过docker安装或者编译安装 ，相对来说docker安装比较方便。
- #### docker安装
- 拉取镜像
- ``` shell
  docker pull redis:latest
  ```
  运行
  ``` shell
   docker run -itd --name redis-test -p 6379:6379 redis
  ```
- #### 编译安装
- 安装redis之前先安装C++编译环境，gcc
- 安装gcc
- ```shell
  yum -y install gcc-c++
  ```
  去官网下载使用wget命令获取redis最新压缩包 ，官网也提供了安装命令。tar命令解压（建议解压到opt路径下），cd到解压的路径，使用make命令编译。
  
  ``` shell
  wget http://download.redis.io/releases/redis-6.0.8.tar.gz
  mv  redis-6.0.8.tar.gz /opt
  tar -xzf redis-6.0.8.tar.gz
  cd redis-6.0.8
  make
  ```
    
  程序已经默认安装好在/usr/loacl/bin下了
- ### 启动
  redis 默认并不是后台启动 ，需要修改config中 **daemonize**属性改为yes
- ```shell
  redis-server  redis.config
  ```
- ### 使用redis客户端连接
  ```
  reids-cli -h 127.0.0.1  -p 6379
  ```
  cli连接后关闭redis
  ```
  shutdown
  ```