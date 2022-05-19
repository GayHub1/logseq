- ### Kibana安装
    
  	1. 从[官网](https://www.elastic.co/cn/downloads/kibana)下载对应系统安装包
  	2. 执行`bin/kibana` (or `bin\kibana.bat` on Windows)文件启动
  	3. 访问 http://localhost:5601 输入上文的ElasticSearchToken即可
  
   如果token过期在es中运行以下命令重新创建token
   ``` ssh
   elasticsearch-create-enrollment-token -s kibana --url "https://127.0.0.1:9200"
   ```