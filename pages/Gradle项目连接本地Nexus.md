- repositories下添加自定义maven仓库
	- ```gradle
	  repositories {
	      maven {
	          url "http://127.0.0.1:8081/repository/maven-public/"
	      }
	  }
	  ```