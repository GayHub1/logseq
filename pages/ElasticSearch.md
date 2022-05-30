- # Elasticsearch 核心技术与实战
  collapsed:: true
	- ElasticSearch起源于Lucene的一款搜索引擎.具有**易扩展**,**高可用**的特点.支持不同的节点类型.支持多种语言类库接入.常用于**实时**搜索.分析.
- ###  ELK安装
  collapsed:: true
	- [[ELK Windows安装]]
	- [[ELK docker-compose 安装]]
	- [[报错]]
- ### 安装与查看插件
  collapsed:: true
	- 查看插件
	  ```
	  .\bin\elasticsearch-plugin list
	  ```
	  安装插件
	  ```
	  .\bin\elasticsearch-plugin install analysis-icu
	  ```
	- Kibana 相关插件 https://www.elastic.co/guide/en/kibana/current/known-plugins.html
- ## 基本概念
  collapsed:: true
	- 与关系型数据库简单对比
	  
	  | RDBMS  | Elasticsearch |
	  | ------ | ------------- |
	  | Table  | Index(Type)   |
	  | Row    | Document      |
	  | Column | Filed         |
	  | Schema | Mapping       |
	  | SQL    | DSL           |
### 文档
collapsed:: true
	- Elasticsearch 是面向文档的，文档是所有可搜索数据的最小单位
	- 文档会被序列化成JSON格式，保存在Elasticsearch中
	- 每个文档都有一个Unique ID
	- RESTAPI
		- 创建文档
		  collapsed:: true
			- 指定id存在时就报错
			- ```
			  PUT users/_create/1
			  {
			      "user" : "Jack",
			      "post_date" : "2019-05-15T14:12:12",
			      "message" : "trying out Elasticsearch"
			  }
			  ```
			- 指定id存在时就更新
			- ```
			  POST users/_doc
			  {
			  	"user" : "Mike",	
			      "post_date" : "2019-04-15T14:12:12",
			      "message" : "trying out Kibana"
			  }	
			  ```
			- ```
			  POST users/_doc/1
			  {
			  	"user" : "Mike",	
			      "post_date" : "2019-04-15T14:12:12",
			      "message" : "trying out Kibana"
			  }
			  ```
		- 获取文档
		  collapsed:: true
			- `GET users/_doc/1`
		- 删除文档
		  collapsed:: true
			- `DELETE users/_doc/1`
		- 批量操作
			- bulk
			  collapsed:: true
				- ```
				  POST _bulk
				  {"index":{"_index":"test","_id":"1"}}
				  {"field1":"value1"}
				  {"delete":{"_index":"test","_id":"2"}}
				  {"create":{"_index":"test2","_id":"3"}}
				  {"field1":"value3"}
				  {"update":{"_id":"1","_index":"test"}}
				  {"doc":{"field2":"value2"}}
				  ```
			- mget
			  collapsed:: true
				- ```
				  GET /_mget
				  {
				      "docs" : [
				          {
				              "_index" : "test",
				              "_id" : "1",
				              "_source" : false
				          },
				          {
				              "_index" : "test",
				              "_id" : "2",
				              "_source" : ["field3", "field4"]
				          },
				          {
				              "_index" : "test",
				              "_id" : "3",
				              "_source" : {
				                  "include": ["user"],
				                  "exclude": ["user.location"]
				              }
				          }
				      ]
				  }
				  ```
			- msearch
			  collapsed:: true
				- ```
				  POST kibana_sample_data_ecommerce/_msearch
				  {}
				  {"query" : {"match_all" : {}},"size":1}
				  {"index" : "kibana_sample_data_flights"}
				  {"query" : {"match_all" : {}},"size":2}
				  ```
		-
	-
### 索引
collapsed:: true
	- Index - 索引是文档的容器，是一类文档的结合
	  collapsed:: true
		- Index体现了逻辑空间的概念：每个索引都有自己的Mapping定义，用于定义包含的文档的字段名和字段类型
		- Shard体现了物理空间的概念：索引中的数据分散在Shard上
	- 索引的 Mapping 与 Settings
	  collapsed:: true
		- Mapping 定义文档字段的类型
		- Setting 定义不同的数据分布
	- RESTAPI
		- 查看索引相关信息
		  `GET kibana_sample_data_ecommerce`
		- 查看索引的文档总数
		  `GET kibana_sample_data_ecommerce/_count`
		- 查看节点**indices**状态(查看集群中该索引情况)
		  `GET /_cat/indices/kibana*?v&s=index`
		- 查看状态为绿的索引
		  `GET /_cat/indices?v&health=green`
		- 查看具体的字段
		  `GET /_cat/indices/kibana*?pri&v&h=health,index,pri,rep,docs.count,mt`
		- 索引具体内容使用情况
		  GET /_cat/indices?v&h=i,tm&s=tm:desc
### 节点
collapsed:: true
	- #### Master-eligible nodes 和 Master Node
	  collapsed:: true
		- 每个节点启动后，默认就是一个Master eligible节点
			- 可以设置node.master:false禁止
		- Master-eligible节点可以参加选主流程，成为Master节点
		- 当第一个节点启动时候，它会将自己选举成Master节点
		- 每个节点上都保存了集群的状态，只有Master节点才能修改集群的状态信息
			- 集群状态（Cluster State),维护了一个集群中，必要的信息
				- 所有的节点信息
				- 所有的索引和其相关的Mapping与Setting信息
				- 分片的路由信息
	- #### Data Node & Coordinating Node
	  collapsed:: true
	  
	  Data Node
		- 可以保存数据的节点，叫做Data Node。负责保存分片数据。在数据扩展上起到了
		  至关重要的作用
		- Coordinating Node
			- 负责接受Client的请求，将请求分发到合适的节点，最终把结果汇集到一起
			- 每个节点默认都起到了Coordinating Node的职责
	- #### Hot & Warm Node
	  collapsed:: true
		- 不同硬件配置的Data Node,用来实现Hot&Warm架构，降低集群部署的成本
	- #### Machine Learning Node
	  collapsed:: true
		- 负责跑机器学习的Job,用来做异常检测
	- #### Tribe Node
	  collapsed:: true
		- Tribe Node连接到不同的Elasticsearch集群，并且支持将这些集群当成一个单独的集群处理5.3开始使用Cross Cluster Serarch)
	- RESTAPI
	- 列出所有node 以及node节点机器状态
	  `GET /_cat/nodes?v`
	- | ip |    heap.percent |   ram.percent |   cpu |   load_1m |   load_5m |   load_15m |   node.role  |  master |name |
	  | :----: | :----------: | :----: | :----: | :----: | :----: | :----: | :----: | :----: |
	  | 10.42.4.198  |   33      |      64  |   3   |   1.82   |   1.81   |    1.43  | himrst    |  -   |     instance-0000000001 |
	  | 10.42.6.65  |            49    |        66  |   3  |    1.69   |   1.78   |    1.62  | himrst |     *   |     instance-0000000000 |
	  | 10.42.5.59   |           70    |        82   |  0    |  1.47    |  2.18  |     2.59 |  mv       |     -     |     tiebreaker-0000000002 |
	  * **heap.percent** 堆内存占用百分比
	  * **ram.percent** 内存占用百分比
	  * **cpu** CPU占用百分比
	  * **master** *表示节点是集群中的主节点
	  * **name** 节点名
	- `GET /_cat/nodes?v&h=id,ip,port,v,m`
	-
	-
- ### 集群
  collapsed:: true
  :LOGBOOK:
  CLOCK: [2022-05-28 Sat 21:28:06]--[2022-05-28 Sat 21:28:07] =>  00:00:01
  :END:
	- 不同的集群通过不同的名字来区分，默认名字“elasticsearch”
	- 通过配置文件修改，或者在命令行中-E cluster.name=geektime进行设定
	  一个集群可以有一个或者多个节点
	- RESTAPI
		- 查看集群健康情况
		- ```
		  GET _cluster/health
		  GET _cluster/health?level=shards
		  GET /_cluster/health/kibana_sample_data_ecommerce,kibana_sample_data_flights
		  GET /_cluster/health/kibana_sample_data_flights?level=shards
		  ```
		- ![image.png](../assets/image_1653748056183_0.png)
	-
### 分片
collapsed:: true
	- 主分片(Primary Shard )，用以解决数据水平扩展的问题。通过主分片，可以将数据分布到集群内的所有节点之上
		- 一个分片是一个运行的Lucene的实例
		- 主分片数在索引创建时指定，后续不允许修改，除非Reindex
	- ·副本(Replica Shard)，用以解决数据高可用的问题。分片是主分片的拷贝
		- 副本分片数，可以动态题调整
		- 增加副本数，还可以在一定程度上提高服务的可用性（读取的吞吐）
	- 对于生产环境中分片的设定，需要提前做好容量规划
		- 分片数设置过小
			- 导致后续无法增加节点实现水品扩展
			- 单个分片的数据量太大，导致数据重新分配耗时
		- 分片数设置过大，7.0开始，默认主分片设置成1,解决了over-sharding的问题
			- 影响搜索结果的相关性打分，影响统计结果的准确性
			- 单个节点上过多的分片，会导致资源浪费，同时也会影响性能
	- RESTAPI
	- ```
	  GET _cat/shards
	  GET _cat/shards?h=index,shard,prirep,state,unassigned.reason
	  ```
	-
### 倒排索引
collapsed:: true
	- 单词词典（Term Dictionary),记录所有文档的单词，记录单词到倒排列表的关联关系
		- 单词词典一般比较大，可以通过B+树或哈希拉链法实现，以满足高性能的插入与查询
	- 倒排列表（Posting List)-记录了单词对应的文档结合，由倒排索引项组成
	  collapsed:: true
		- 倒排索引项（Posting)
			- 文档ID
			- 词频TF-该单词在文档中出现的次数，用于相关性评分
			- 位置（Position)-单词在文档中分词的位置。用于语句搜索（phrase query
			- 偏移（Offset)-记录单词的开始结束位置，实现高亮显示
			  
			    ![image-20220504182115226](https://cdn.jsdelivr.net/gh/GayHub1/images@master/img/image-20220504182115226.png)
	- collapsed:: true
		- ```
		  POST _analyze
		  {
		    "analyzer": "standard",
		    "text": "Mastering Elasticsearch"
		  }
		  ```
		-
- ### 分词器
  collapsed:: true
	- 常见分词器
	  
	  | 名称                                                         | 作用                                                         |
	  | ------------------------------------------------------------ | ------------------------------------------------------------ |
	  | Simple Analyzer                                              | 按照非字母切分（符号被过滤），小写处理                       |
	  | Stop Analyzer                                                | 小写处理，停用词过滤（the，a，is）                           |
	  | Whitespace Analyzer                                          | 按照空格切分，不转小写                                       |
	  | Patter Analyze                                               | 正则表达式，默认 \W+ (非字符分隔)                            |
	  | Keyword Analyzer                                             | 不分词，直接将输入当作输出                                   |
	  | Language                                                     | 提供了30多种常见语言的分词器                                 |
	  | [IK]( https://github.com/medcl/elasticsearch-analysis-ik)    | 支持自定义词库，支持热更新分词字典                           |
	  | [THULAC](https://github.com/microbun/elasticsearch-thulac-plugin) | THU Lexucal Analyzer for Chinese,清华大学自然语言处理和社会人文计算实验室的一套中文分词器 |
	  
	  **安装**
	  
	  1. 将[IK分词器压缩包]( https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v7.1.0/elasticsearch-analysis-ik-7.1.0.zip)映射进容器内/usr/share/elasticsearch/plugins目录下，修改在docker-compose文件，在**volumes**下添加
	    ``` ./ELKConfig/elasticsearch/plugins:/usr/share/elasticsearch/plugins #插件文件挂载```
	  2. 也可以容器内命令安装
	     ```
	     ./bin/elasticsearch-plugin install  https://github.com/medcl/elasticsearch-analysis-ik/releases/downlo
	     ad/v7.1.0/elasticsearch-analysis-ik-7.1.0.zip
	     ```
	    3. 重启所有es容器
	    4. 访问http://127.0.0.1:9200/_cat/plugins 查看插件是否被加载进去
	     ![image-20220504213020492](C:\Users\94241\AppData\Roaming\Typora\typora-user-images\image-20220504213020492.png)
	    测试
	    ```
	    POST _analyze
	    {
	    "analyzer": "ik_smart",
	    "text": "这个苹果不大好吃"
	    }
	    ```
	  
	    如果安装插件后重启容器失败，可以将插件压缩包解压到plugins文件夹下ik文件夹
	- **RESTAPI**
	  collapsed:: true
		- ```
		  #Simple Analyzer – 按照非字母切分（符号被过滤），小写处理
		  #Stop Analyzer – 小写处理，停用词过滤（the，a，is）
		  #Whitespace Analyzer – 按照空格切分，不转小写
		  #Keyword Analyzer – 不分词，直接将输入当作输出
		  #Patter Analyzer – 正则表达式，默认 \W+ (非字符分隔)
		  #Language – 提供了30多种常见语言的分词器
		  #2 running Quick brown-foxes leap over lazy dogs in the summer evening
		  
		  #查看不同的analyzer的效果
		  #standard
		  GET _analyze
		  {
		    "analyzer": "standard",
		    "text": "2 running Quick brown-foxes leap over lazy dogs in the summer evening."
		  }
		  
		  #simpe
		  GET _analyze
		  {
		    "analyzer": "simple",
		    "text": "2 running Quick brown-foxes leap over lazy dogs in the summer evening."
		  }
		  
		  
		  GET _analyze
		  {
		    "analyzer": "stop",
		    "text": "2 running Quick brown-foxes leap over lazy dogs in the summer evening."
		  }
		  
		  
		  #stop
		  GET _analyze
		  {
		    "analyzer": "whitespace",
		    "text": "2 running Quick brown-foxes leap over lazy dogs in the summer evening."
		  }
		  
		  #keyword
		  GET _analyze
		  {
		    "analyzer": "keyword",
		    "text": "2 running Quick brown-foxes leap over lazy dogs in the summer evening."
		  }
		  
		  GET _analyze
		  {
		    "analyzer": "pattern",
		    "text": "2 running Quick brown-foxes leap over lazy dogs in the summer evening."
		  }
		  
		  
		  #english
		  GET _analyze
		  {
		    "analyzer": "english",
		    "text": "2 running Quick brown-foxes leap over lazy dogs in the summer evening."
		  }
		  
		  
		  POST _analyze
		  {
		    "analyzer": "icu_analyzer",
		    "text": "他说的确实在理”"
		  }
		  
		  
		  POST _analyze
		  {
		    "analyzer": "standard",
		    "text": "他说的确实在理”"
		  }
		  
		  
		  POST _analyze
		  {
		    "analyzer": "icu_analyzer",
		    "text": "这个苹果不大好吃"
		  }
		  ```
- ### Search API
  collapsed:: true
	- 指定索引
	  
	  ![image-20220504214925983](https://cdn.jsdelivr.net/gh/GayHub1/images@master/img/image-20220504214925983.png){:height 324, :width 747}
	- #### URL Search
	  
	  ![Replaced by Image Uploder](https://cdn.jsdelivr.net/gh/GayHub1/images@master/img/image-20220518233406287_1653743572042_0.png){:height 156, :width 747}
	- q:  指定查询语句，使用Query String Syntax
	- df: 默认字段，不指定时，会对所有字段进行查询
	- Sort 排序/from和size用于分页
	- Profile 可以查看查询是如何被执行的
	- ```
	  指定字段
	  GET /movies/_search?q=title:2012&sort=year:desc&from=0&size=10&timeout=1s
	  {
	  	"profile":"true"
	  }
	  ```
	- Term V.S Phrase
		- Beautiful Mind 等效于 Beautiful OR  Mind    title:(Beautiful AND Mind)
		- “Beautiful Mind”，等效于 Beautiful AND Mind。Phrase查询，还要求前后顺序保持一致 title="Beautiful Mind"
		- ```
		  # 查找美丽心灵, Mind为泛查询
		  GET /movies/_search?q=title:Beautiful Mind
		  {
		  	"profile":"true"
		  }
		  
		  #使用引号，Phrase查询
		  GET /movies/_search?q=title:"Beautiful Mind"
		  {
		  	"profile":"true"
		  }
		  
		  #分组，Bool查询
		  GET /movies/_search?q=title:(Beautiful Mind)
		  {
		  	"profile":"true"
		  }
		  
		  ```
	- 布尔操作
		- AND/OR/NOT或者&&/ll/!
		- 必须大写
			- title:(matrix NOT reloaded)
		- ```
		  #布尔操作符
		  # 查找美丽心灵
		  GET /movies/_search?q=title:(Beautiful AND Mind)
		  {
		  	"profile":"true"
		  }
		  
		  # 查找美丽心灵
		  GET /movies/_search?q=title:(Beautiful NOT Mind)
		  {
		  	"profile":"true"
		  }
		  
		  ```
	- 分组
		- \+ 表示must
		- \- 表示must_not
		- title:(+matrix -reloaded)
		- ```
		  # 查找美丽心灵  %2B 代表+号
		  GET /movies/_search?q=title:(Beautiful %2BMind)
		  {
		  	"profile":"true"
		  }
		  ```
	- 范围查询
		- 区间表示：[] 闭区间 ，{} 开区间
			- year:{2019 TO 2018}
			- year:[* TO 2018]
			- ```
			  #范围查询 ,区间写法
			  GET /movies/_search?q=title:beautiful AND year:[2002 TO 2018%7D
			  {
			  	"profile":"true"
			  }
			  
			  ```
	- 算数符号
		- year:&gt;2010
		- year:(>2010 && &lt;=2018)
		- year:(+>2010 +<=2018)
	- 通配符查询（通配符查询效率低，占用内存大，不建议使用。特别是放在最前面）
		- ？代表1个字符，* 代表 0 或 多个字符
			- title:mi?d
			- title:be*
		- ```
		  #通配符查询
		  GET /movies/_search?q=title:b*
		  {
		  	"profile":"true"
		  }
		  ```
	- ·正则表达
		- title:[bt]oy
	- 模糊匹配与近似查询
		- title:befutifl~1
		- title:"lord rings"~2
	- ```
	  //模糊匹配&近似度匹配
	  GET /movies/_search?q=title:beautifl~1
	  {
	  	"profile":"true"
	  }
	  
	  GET /movies/_search?q=title:"Lord Rings"~2
	  {
	  	"profile":"true"
	  }
	  ```
- ### Request Body Search
  collapsed:: true
	- ![image-20220518232842975](https://cdn.jsdelivr.net/gh/GayHub1/images@master/img/image-20220518232842975.png)
	- **RESTAPI**
	- ```
	  POST /kibana_sample_data_ecommerce/_search
	  {
	    "from":10,
	    "size":20,
	    "sort":[{"order_date":"desc"}],
	    "_source":["order_date"],
	    "query":{
	      "match_all": {}
	    }
	  }
	  
	  #脚本字段
	  GET kibana_sample_data_ecommerce/_search
	  {
	    "script_fields": {
	      "new_field": {
	        "script": {
	          "lang": "painless",
	          "source": "doc['order_date'].value+'hello'"
	        }
	      }
	    },
	    "query": {
	      "match_all": {}
	    }
	  }
	  #Term查询 默认是有其中一个 OR关系
	  POST movies/_search
	  {
	    "query": {
	      "match": {
	        "title": "last christmas"
	      }
	    }
	  }
	  
	  # 两个都必须有
	  POST movies/_search
	  {
	    "query": {
	      "match": {
	        "title": {
	          "query": "last christmas",
	          "operator": "and"
	        }
	      }
	    }
	  }
	  #Phrase查询 slop：允许中间插入指定数量的Team
	  POST movies/_search
	  {
	    "query": {
	      "match_phrase": {
	        "title":{
	          "query": "one love",
	          "slop": 1
	  
	        }
	      }
	    }
	  }
	  ```
	-
- ### QueryString&SimpleQueryString
  collapsed:: true
	- ```
	  POST users/_search
	  {
	    "query": {
	      "query_string": {
	        "default_field": "name",
	        "query": "Ruan AND Yiming"
	      }
	    }
	  }
	  
	  
	  POST users/_search
	  {
	    "query": {
	      "query_string": {
	        "fields":["name","about"],
	        "query": "(Ruan AND Yiming) OR (Java AND Elasticsearch)"
	      }
	    }
	  }
	  
	  
	  #Simple Query 默认的operator是 Or 不支持 AND 会被识别成字符串 用default_operator来指定AND
	  POST users/_search
	  {
	    "query": {
	      "simple_query_string": {
	        "query": "Ruan AND Yiming",
	        "fields": ["name"]
	      }
	    }
	  }
	  
	  
	  POST users/_search
	  {
	    "query": {
	      "simple_query_string": {
	        "query": "Ruan Yiming",
	        "fields": ["name"],
	        "default_operator": "AND"
	      }
	    }
	  }
	  
	  
	  GET /movies/_search
	  {
	  	"profile": true,
	  	"query":{
	  		"query_string":{
	  			"default_field": "title",
	  			"query": "Beafiful AND Mind"
	  		}
	  	}
	  }
	  
	  
	  # 多fields
	  GET /movies/_search
	  {
	  	"profile": true,
	  	"query":{
	  		"query_string":{
	  			"fields":[
	  				"title",
	  				"year"
	  			],
	  			"query": "2012"
	  		}
	  	}
	  }
	  
	  
	  
	  GET /movies/_search
	  {
	  	"profile":true,
	  	"query":{
	  		"simple_query_string":{
	  			"query":"Beautiful +mind",
	  			"fields":["title"]
	  		}
	  	}
	  }	
	  ```
- ### Mapping
  collapsed:: true
	- 定义
		- Mapping类似数据库中的schema的定义，作用如下
		   * 定义索引中的字段的名称
		   * 定义字段的数据类型，例如字符串，数字，布尔......
		  * 字段，倒排索引的相关配置，(Analyzed or Not Analyzed,Analyzer)
		- Mapping会把JSON文档映射成Lucene所需要的扁平格式
		- 一个Mapping属于一个索引的Type
		- 每个文档都属于一个Type
		  * 一个Type有一个Mapping定义
		  * 7.0开始，不需要在Mapping定义中指定type信息
	- 数据类型
		- 简单类型
		  * Text/Keyword
		  * Date
		  * Integer / Floating
		  *  Boolean
		  * Pv4&IPv6
		- 复杂类型-对象和嵌套对象
		  * 对象类型/嵌套类型
		- 特殊类型
		  *  geo_point &  geo_shape/percolator
	- Dynamic Mapping
		- 在写入文档时候，如果索引不存在，
		  会自动创建索引
		- Dynamic Mapping的机制，使得
		  我们无需手动定义Mappings。
		  Elasticsearch 会自动根据文档信
		  息（JSON类型），推算出字段的类型
	- 能否修改Mapping字段类型(倒排索引有无建立，建立了就只能reindex 才能被索引)
		- 新增加字段
		  * Dynamic设为true时，一旦有新增字段的文档写入，Mapping也同时被
		  更新
		  * Dynamic设为false,Mapping不会被更新，新增字段的数据无法被索引，
		  但是信息会出现在_source中返回到标签页
		  * Dynamic设置成Strict,文档写入失败
		- 对已有字段，一旦已经有数据写入，就不再支持修改字段定义
			- Lucene实现的倒排索引，一旦生成后，就不允许修改
		- 如果希望改变字段类型，必须ReindexAPI,重建索引
			- 原因
			  * 如果修改了字段的数据类型，会导致已被索引的属于无法被搜索
			  * 但是如果是增加新的字段，就不会有这样的影响
	- 当dynamic被设置成false时候，存在新增字段的数据写入，该数据可以被索引，但是新增字段被丢弃
	- 当设置成 Strict模式时候，数据写入直接出错
	- **Index** ： 控制当前字段是否被索引。默认为true。如果设置成 false,该字段不可被搜索
	- Index Options
		- 四种不同级别的Index Options配置，可以控制倒排索引记录的内容
		  * docs 记录doc id
		  * freqs 记录 doc id 和 term frequencies
		  * positions 记录doc id/term frequencies/term position
		  * offsets  记录docid/termfrequencies/termposistion/characteroffects
		- Text类型默认记录**postions**,其他默认为**docs**
		- 记录内容越多，占用存储空间越大
	- null_Value : 对空值赋一个空值对应属性，使得可以被搜索
	- copy_to
	   * copy_to将字段的数值拷贝到目标字段，实现类似_all的作用
	  	* copy_to的目标字段不出现在_source中
	- Elasticsearch中不提供专门的数组类型。但是任何字段，都可以包含多个相同类类型的数值
	- 多字段特性
	  * 使用不同的**analyzer**
	- Excat values V.S Full Text
		- Exact Value:包括数字/日期/具体一个字符串（例如“Apple Stare")
			- Elasticseach 中的 keyword
			  *  不做特殊的分词处理
			  * 字段与索引一一对应
	- 全文本，非结构化的文本数据
	  * （Elasticsearch 中的 text）
- ### IndexTemplate和DynamicTemplate
  collapsed:: true
	- IndexTemplate
		- Index Templates - 帮助你设定 Mappings 和 Settings,并按照一定的规则，自动匹配到新创建的索引之上
		  * 模版仅在一个索引被新创建时，才会产生作用。修改模版不会影响已创建的索引
		  * 你可以设定多个索引模版，这些设置会被“merge”在一起
		  * 你可以指定“order”的数值，控制“merging”的过程
		- 当一个索引被新创建时
		  * 应用Elasticsearch默认的settings和mappings
		  * 应用order数值低的IndexTemplate中的设定
		  * 应用order高的IndexTemplate中的设定，之前的设定会被覆盖
		  * 应用创建索引时，用户所指定的Settings和Mappings,并覆盖之前模版中的设定
		- **RESTAPI**
			- ```
			  PUT /_template/template_test
			  {
			      "index_patterns" : ["test*"],
			      "order" : 1,
			      "settings" : {
			      	"number_of_shards": 1,
			          "number_of_replicas" : 2
			      },
			      "mappings" : {
			      	"date_detection": false,
			      	"numeric_detection": true
			      }
			  }
			  GET testtemplate/_mapping
			  get testtemplate/_settings
			  PUT testmy
			  {
			  	"settings":{
			  		"number_of_replicas":5
			  	}
			  }
			  ```
	- DynamicTemplate
		- 根据 Elasticsearch识别的数据类型，结合字段名称，来动态设定字段类型
		  * *所有的字符串类型都设定成Keyword,或者关闭keyword字段
		  * is开头的字段都设置成 boolean
		  * long_开头的都设置成long类型
	- ```
	  PUT my_index
	  {
	    "mappings": {
	      "dynamic_templates": [
	              {
	          "strings_as_boolean": {
	            "match_mapping_type":   "string",
	            "match":"is*",
	            "mapping": {
	              "type": "boolean"
	            }
	          }
	        },
	        {
	          "strings_as_keywords": {
	            "match_mapping_type":   "string",
	            "mapping": {
	              "type": "keyword"
	            }
	          }
	        }
	      ]
	    }
	  }
	  ```
	  * Dynamic Tempate 是定义在在某个索引的 Mapping 中
	  * Template有一个名称
	  * 匹配规则是一个数组
	  * 为匹配到字段设置 Mapping
	- **RESTAPI**
	- ```
	  PUT my_index
	  {
	    "mappings": {
	      "dynamic_templates": [
	        {
	          "full_name": {
	            "path_match":   "name.*",
	            "path_unmatch": "*.middle",
	            "mapping": {
	              "type":       "text",
	              "copy_to":    "full_name"
	            }
	          }
	        }
	      ]
	    }
	  }
	  
	  PUT my_index/_doc/1
	  {
	    "name": {
	      "first":  "John",
	      "middle": "Winston",
	      "last":   "Lennon"
	    }
	  }
	  
	  GET my_index/_search?q=full_name:John
	  get my_index/_settings
	  get my_index/_mapping
	  ```
- **Aggregation**
	- Elasticsearch除搜索以外，提供的针对ES数据进行统计分析的功能
	  * 实时性高
	  * 而Hadoop需要 (T+1)
	- 通过聚合，我们会得到一个数据的概览，是分析和总结全套的数据，而不是寻找单个文档
	- 高性能，只需要一条语句，就可以从 Elasticsearch得到分析结果，无需在客户端自己去实现分析逻辑
	- 分类
	  collapsed:: true
		- Bucket Aggregation
		  collapsed:: true
			- 一些列满足特定条件的文档的集合
		- Metric Aggregation
			- 一些数学运算，可以对文档字段进行统计分析
			- Metric 会基于数据集计算结果，除了支持在字段上进行计算，同样也支持在脚本
			  (painless script)产生的结果之上进行计算
			- 大多数 Metric 是数学计算，仅输出一个值
			  *  min/max/sum/avg/cardinality
			- 部分metric支持输出多个数值
			  * stats/percentiles/percentile_ranks
		- Pipeline Aggregation
		  collapsed:: true
			- 对其他的聚合结果进行二次聚合
		- Matrix Aggregration
		  collapsed:: true
			- 支持对多个字段的操作并提供一个结果矩阵
	- **RESTAPI**
	  collapsed:: true
		- ```
		  #按照目的地进行分桶统计
		  GET kibana_sample_data_flights/_search
		  {
		  	"size": 0,
		  	"aggs":{
		  		"flight_dest":{
		  			"terms":{
		  				"field":"DestCountry"
		  			}
		  		}
		  	}
		  }
		  
		  
		  
		  #查看航班目的地的统计信息，增加平均，最高最低价格
		  GET kibana_sample_data_flights/_search
		  {
		  	"size": 0,
		  	"aggs":{
		  		"flight_dest":{
		  			"terms":{
		  				"field":"DestCountry"
		  			},
		  			"aggs":{
		  				"avg_price":{
		  					"avg":{
		  						"field":"AvgTicketPrice"
		  					}
		  				},
		  				"max_price":{
		  					"max":{
		  						"field":"AvgTicketPrice"
		  					}
		  				},
		  				"min_price":{
		  					"min":{
		  						"field":"AvgTicketPrice"
		  					}
		  				}
		  			}
		  		}
		  	}
		  }
		  
		  
		  
		  #价格统计信息+天气信息
		  GET kibana_sample_data_flights/_search
		  {
		  	"size": 0,
		  	"aggs":{
		  		"flight_dest":{
		  			"terms":{
		  				"field":"DestCountry"
		  			},
		  			"aggs":{
		  				"stats_price":{
		  					"stats":{
		  						"field":"AvgTicketPrice"
		  					}
		  				},
		  				"wather":{
		  				  "terms": {
		  				    "field": "DestWeather",
		  				    "size": 5
		  				  }
		  				}
		  
		  			}
		  		}
		  	}
		  }
		  
		  
		  ```