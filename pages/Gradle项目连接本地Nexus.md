- repositories下添加自定义maven仓库
	- ```gradle
	  repositories {
	      maven {
	          url "http://127.0.0.1:8081/repository/maven-public/"
	      }
	  }
	  ```
- distributionUrl 使用本地文件
	- `distributionUrl=file:///D:/gradle-6.4.1-all.zip`
	- >>  gradle文件[下载地址]
-