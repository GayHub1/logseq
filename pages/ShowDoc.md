- docker-compose 部署
	- ```yaml
	  version: '3.5'
	  services:
	    showdoc:
	      image: star7th/showdoc
	      container_name: showdoc
	      hostname: showdoc
	      restart: always
	      ports:
	        - 4999:80
	      volumes:
	        - ./showdoc_data/html:/var/www/html
	      privileged: true
	      user: root
	  ```
- 数据迁移
	- 复制旧服务器的Sqlite/showdoc.db.php，以及Public/Uploads/下的所有文件（如没有则可忽略之），覆盖到新showdoc目录的相应文件。