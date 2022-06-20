- tags: #[[宝塔]] #[[Hexo]]
- 先前一直只是简单的 用nginx加git部署hexo，但是由于nginx是在安装zabbix时一块儿安装的，nginx找不到安装路径，于是打算从新在阿里云上安装宝塔，再通过宝塔安装 nginx，部署hexo。
## 安装宝塔

一行命令安装宝塔（真香）

```shell
yum install -y wget && wget -O install.sh http://download.bt.cn/install/install_6.0.sh && sh install.sh
```

输入y，并回车。接下来便是等待宝塔面板进行安装。

安装后会显示自动生成的账号密码：类似如下

![宝塔安装成功界面](http://qcyd0eqp3.bkt.clouddn.com/20190802173406483.png)

<!--记得阿里云放行安全组 8888 80 两个端口-->

安装完后登录宝塔，首次登陆会提示我们安装环境，我们勾选左侧包含Nginx的即可。其他的也可以顺带安装好mysql与php。
## git部署

我们在home文件夹下
### 新建两个文件夹git，hexo

```shell
cd ..   //这里是因为默认执行目录是root，需要返回根目录
cd home
mkdir -p git
mkdir -p hexo
cd git
git init --bare hexoBlog.git
```

此时显示一行 

```shell
Initialized empty Git repository in /home/hexoBlog.git/
```

代表我们的git储存库已经创建成功。

然后 在目录 `/home/git/hexoBlog.git/hooks`下
### 新建文件post-receive

```shell
cd /home/git/hexoBlog.git/hooks  
vim post-receive
```

写入以下代码：

```shell
#!/bin/bash
git --work-tree=/home/hexo --git-dir=/home/git/hexoBlog.git checkout -f
```

给post-receive权限

```shell
chmod +x /home/git/hexoBlog.git/hooks/post-receive
```
## 配置Nginx

宝塔面板默认的nginx配置文件在根目录->www->serve->nginx->conf下这里我们通过 include 的方式导入博客 ；

在vhost文件夹下
### 新建文件blog.conf

```Shell 
vim blog.conf
```

文件内容为

```nginx
server{
	listen    80;
	root /home/www/website;这里填博客目录存放的地址
	server_name 这里填域名如(www.baidu.com) 如果暂时没有域名就填阿里云的公网ip，以后有了再改回来;
	location /{
	}
}
```
### 修改nginx.conf

在http下使用include添加刚才的blog.conf文件；
### 重启Nginx服务

```
service nginx restart
```
## 本地Hexo配置

按照这个格式配置，如果你只推送到aliyun就配置那一行就行了，推送到多个平台务必按照以下格式进行填写（注意缩进）

```yaml
deploy:
type: git
repo:
    github: git@github.com:HowarZheng/howarzheng.github.io.git,master
    coding: https://git.dev.tencent.com/xigzheng/xigzheng.git,master
    #gitee: https://gitee.com/howarzheng_001/howarzheng_001.git,master
    aliyun: root@120.55.161.99:/home/git/hexoBlog
```

然后本地运行命令

```shell
hexo d
```

后输入密码即可将本地的文件推到阿里云了
## git报错

由于阿里云初始化了系统磁盘，所以git本地在推送时 会出现一个报错

```shell
WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!   
```

![WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED](http://qcyd0eqp3.bkt.clouddn.com/image-20200708105156654.png)
### 出现原因

更换系统后，需要重新匹配远程服务器信息。
### 解决方案

把**/c/Users/Daemon/.ssh/known_hosts** 文件中(服务器)地址ip的公钥删除，再次输入密码推送就可以了。

![推送成功图片](http://qcyd0eqp3.bkt.clouddn.com/image-20200708105431408.png)
-