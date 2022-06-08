- ![image.png](../assets/image_1654652170641_0.png)
- ## docker-compose
	- 目录结构
		- ![image.png](../assets/image_1654652794942_0.png)
	- master-1文件夹 内 有两个文件
		- Dockerfile  -- docker 镜像打包文件
			- ```Dockerfile  
			  FROM mysql:5.7.17
			  MAINTAINER harrison
			  ADD ./master-1/my.cnf /etc/mysql/my.cnf
			  ```
		- my.cnf master