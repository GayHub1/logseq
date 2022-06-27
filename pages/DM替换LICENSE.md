- docker
  collapsed:: true
	- key的位置,直接停服务，替换文件，启动
		- ```sh
		  /home/dmdba/dmdbms/bin/dm.key
		  ```
- 在线更新
	- 1. 改个名dm.key放bin目录下
	  2. 还需要把权限改为dmdba的
	  3. 把key替换完后，需要在数据库执行这个`SP_LOAD_LIC_INFO();`
	  4. 查看新的过期时间`select * from V$LICENSE`
-