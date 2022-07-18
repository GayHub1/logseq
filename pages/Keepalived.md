- 高性能的服务器高可用或热备解决方案
- 主要来防止服务器单点故障的发生问题
- 以VRRP协议为实现基础，用VRRP协议来实现高可用性
- keepalived配置指南
	- 安装keepalived
	  collapsed:: true
		- ```
		  yum install keepalived -y
		  ```
	- 编辑keepalived配置文件
	  collapsed:: true
		- ```sh
		  vi /etc/keepalived/keepalived.conf
		  ```
	- 主机配置文件
	  collapsed:: true
		- ```sh
		  ! Configuration File for keepalived
		  
		  global_defs {
		     router_id mq04
		     vrrp_skip_check_adv_addr
		     vrrp_strict
		     vrrp_garp_interval 0
		     vrrp_gna_interval 0
		  }
		  
		  vrrp_script chk_haproxy {
		      script "/etc/keepalived/haproxy_check.sh"  ##执行脚本位置
		      interval 2  ##检测时间间隔
		      weight -20  ##如果条件成立则权重减20
		  }
		  
		  vrrp_instance VI_1 {
		      state MASTER
		      interface ens33
		      virtual_router_id 51
		      mcast_src_ip 192.168.57.133
		      priority 100
		      advert_int 1
		      authentication {
		          auth_type PASS
		          auth_pass 1111
		      }
		      virtual_ipaddress {
		          192.168.57.233
		      }
		      track_script {
		          chk_haproxy
		      }
		  }
		  ```
	- 热备机配置文件
	  collapsed:: true
		- ```sh
		  global_defs {
		   router_id mq04
		   vrrp_skip_check_adv_addr
		   vrrp_strict
		   vrrp_garp_interval 0
		   vrrp_gna_interval 0
		  }
		  - vrrp_script chk_haproxy {
		  script "/etc/keepalived/haproxy_check.sh"  ##执行脚本位置
		  interval 2  ##检测时间间隔
		  weight -20  ##如果条件成立则权重减20
		  }
		  - vrrp_instance VI_1 {
		  state BACKUP
		  interface ens33
		  virtual_router_id 51
		  mcast_src_ip 192.168.57.131
		  priority 50
		  advert_int 1
		  authentication {
		      auth_type PASS
		      auth_pass 1111
		  }
		  virtual_ipaddress {
		      192.168.57.233
		  }
		  track_script {
		      chk_haproxy
		  }
		  }
		  ```
	- 健康检测脚本
	  collapsed:: true
		- ```sh
		  vi /etc/keepalived/haproxy_check.sh
		  ```
		  ```bash
		  #!/bin/bash
		  COUNT=`ps -C haproxy --no-header |wc -l`
		  if [ $COUNT -eq 0 ];then
		    systemctl start haproxy
		    sleep 2
		    if [ `ps -C haproxy --no-header |wc -l` -eq 0 ];then
		        systemctl stop keepalived
		    fi
		  fi
		  ```
	- 修改健康检测脚本执行权限
		- ```sh
		  chmod +x /etc/keepalived/haproxy_check.sh
		  ```
	- 启动keepalived
		- ```sh
		  systemctl start keepalived
		  ```
	- 做故障转移实验时，关闭keepalived即可
		- ```sh
		  systemctl stop keepalived
		  ```