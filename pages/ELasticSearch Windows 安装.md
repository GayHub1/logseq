- ### Elasticsearch 安装
    
  	1. 从[官网](https://www.elastic.co/downloads/elasticsearch)下载对应系统安装包
  	2. 修改配置(建议jvm的 Xmx与Xms内存设置一样且不超过机器内存的一半或30G)
  	3. 执行`bin/elasticsearch` (or `bin\elasticsearch.bat` )文件启动
  
  4. 留意命令行输出 将显示的密码和注册令牌(token)保存起来
    
  ``` ssh
   ------------------------------------------------------------------------------------------------------------------------
   -> Elasticsearch security features have been automatically configured!
   -> Authentication is enabled and cluster connections are encrypted.
   
   ->  Password for the elastic user (reset with `bin/elasticsearch-reset-password -u elastic`):
     S1t0X*uv0ExZLS-2hRnd
   
   ->  HTTP CA certificate SHA-256 fingerprint:
     7ab1e8853b0cf3b49c9d9d4f1d7173b00082a7f38517bc0bbcbe7c6f0f1bbf45
   
   ->  Configure Kibana to use this cluster:
   * Run Kibana and click the configuration link in the terminal when Kibana starts.
   * Copy the following enrollment token and paste it into Kibana in your browser (valid for the next 30 minutes):
     eyJ2ZXIiOiI4LjEuMyIsImFkciI6WyIxNzIuMjQuMTYwLjE6OTIwMCIsIjE5Mi4xNjguMjAwLjIwMjo5MjAwIiwiMTcyLjI4LjY0LjE6OTIwMCJdLCJmZ3IiOiI3YWIxZTg4NTNiMGNmM2I0OWM5ZDlkNGYxZDcxNzNiMDAwODJhN2YzODUxN2JjMGJiY2JlN2M2ZjBmMWJiZjQ1Iiwia2V5IjoieXlEQ2FZQUJjUHBKMXFfTkF4bGM6QXc5MW5Gc01TNGV5aFdzd1A0UzhJQSJ9
   
   ->  Configure other nodes to join this cluster:
   * On this node:
     - Create an enrollment token with `bin/elasticsearch-create-enrollment-token -s node`.
     - Uncomment the transport.host setting at the end of config/elasticsearch.yml.
     - Restart Elasticsearch.
   * On other nodes:
     - Start Elasticsearch with `bin/elasticsearch --enrollment-token <token>`, using the enrollment token that you generated.
   ------------------------------------------------------------------------------------------------------------------------
   
  ```
    
  	5. 访问[https://127.0.0.1:9200](https://127.0.0.1:9200/)输入 用户名 elastic 与上文密码即可看见已成功启动
- ###
    
  	1. 从[官网](https://www.elastic.co/cn/downloads/kibana)下载对应系统安装包
  	2. 执行`bin/kibana` (or `bin\kibana.bat` on Windows)文件启动
  	3. 访问 http://localhost:5601 输入上文的ElasticSearchToken即可
  
   如果token过期在es中运行以下命令重新创建token
   ``` ssh
   elasticsearch-create-enrollment-token -s kibana --url "https://127.0.0.1:9200"
   ```