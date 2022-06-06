- docker-compose.yaml
	- ```yaml
	  version: "3.4"
	  services:
	    nexus3 :
	      restart: unless-stopped
	      image: sonatype/nexus3
	      container_name: nexus3 
	      volumes:
	      - ./nexus:/var/nexus-data 
	      ports:
	      - "8081:8081"
	  ```
- 查看是否启动成功
	- `docker-compose logs -f --tail 100 nexus3`
	- ![image.png](../assets/image_1654484789076_0.png)