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