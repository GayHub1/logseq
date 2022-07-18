- Java Linux 安装
  id:: 62d5641e-67f7-4578-88bb-5b9eedd323bf
	- [官网下载](https://www.oracle.com/java/technologies/downloads/)
	- ```sh
	  wget https://download.oracle.com/java/18/latest/jdk-18_linux-x64_bin.tar.gz
	  tar -zxf  jdk-18_linux-x64_bin.tar.gz 
	  cp jdk-18.0.1.1/  /usr/local/java
	  vi /etc/profile 
	  #--加入文件末尾--
	  export JAVA_HOME=/usr/local/java
	  export PATH=$PATH:$JAVA_HOME/bin
	  export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
	  export JRE_HOME=$JAVA_HOME/jre
	  #---
	  source /etc/profile
	  ```