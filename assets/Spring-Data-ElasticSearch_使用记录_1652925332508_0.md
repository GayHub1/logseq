

# Spring-Data-ElasticSearch 使用记录

## 前言

Spring-Data-ElasticSearch是Spring data家族中的一员。在RestHighLevelClient基础上进行封装，使其符合Spring-data的规范。提供了ElasticsearchRepository类进行基本的CRUD方法与简单的方法扩展。简化了操作流程。

## 使用流程

### 添加依赖

在项目pom.xml文件中添加Spring-Data-ElasticSearch依赖

```
        <!--集成elasticsearch-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-elasticsearch</artifactId>
        </dependency>
```

### 添加Config配置文件

```
@Configuration
public class ElasticsearchConfig extends AbstractElasticsearchConfiguration {

    @Override
    public RestHighLevelClient elasticsearchClient() {
        final ClientConfiguration clientConfiguration = ClientConfiguration.builder()
                .connectedTo("localhost:9200")
                .build();
        return RestClients.create(clientConfiguration).rest();
    }
}
```

（ TransportClient从 Elasticsearch 7 开始弃用，将在 Elasticsearch 8 中删除。推荐使用**RestHighLevelClient**）

其他配置如添加用户名密码，ssl开启，可查看[官网](https://docs.spring.io/spring-data/elasticsearch/docs/current/reference/html/#elasticsearch.clients.configuration)详细说明。

### 业务中使用

业务中的使用类似于Mybatis Plus，须分别

- 定义modle
- 定义Repository
- 调用对应Repository接口查询

#### 定义modle

创建ElasticSearch对应索引的对象映射。类似Mybatis 中对表的映射

```
@Data
@Accessors(chain = true)
@Document(indexName = "movies")
public class Movie {
    @Id
    private String id;

    @Field(type = FieldType.Text, analyzer = "ik_max_word")
    private String title; //标题

    @Field(type = FieldType.Keyword)
    private List<String> genre;// 分类

    @Field(type = FieldType.Long)
    private Long year; // 年份
}
```

一些常见的注释

- `@Document`：标明该类是ElasticSearch的映射类
  - `indexName`：指定对应的索引名称，支持 SpEL 模板表达式。
  - `createIndex`：项目启动时是当索引不存在时是否创建索引。默认值为 *true*。详情可以参考[使用相应的映射自动创建索引](https://docs.spring.io/spring-data/elasticsearch/docs/current/reference/html/#elasticsearch.repositories.autocreation)
- `@Id`：标识为文档ID对应字段，（交由ElasticSearch生成ID时建议字段类型为String）
- `@Transient`：排除该字段，不映射到ElasticSearch。
- `@Field`：定义字段的属性：
  - `name`：字段的名称，默认使用 Java 字段名称。
  - `type`：字段类型可以是*Text, Keyword, Long, Integer, Short, Byte, Double, Float, Half_Float, Scaled_Float, Date, Date_Nanos, Boolean, Binary*。具体可以看 [Elasticsearch Mapping Types](https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping-types.html)。如果未指定字段类型，则由Elasticsearch 根据第一次保存的值自动生成）。
  - `format`：一个或多个内置日期格式，具体参考[日期格式映射](https://docs.spring.io/spring-data/elasticsearch/docs/current/reference/html/#elasticsearch.mapping.meta-model.date-formats)。
  - `pattern`：一个或多个自定义日期格式，具体参考[日期格式映射](https://docs.spring.io/spring-data/elasticsearch/docs/current/reference/html/#elasticsearch.mapping.meta-model.date-formats)。
  - `analyzer`、，用于指定分词器，官方的默认中文分词器并不好用，常用的是IK分词器，对应。`ik_smart``ik_max_word`
- `@GeoPoint`：将字段标记为*geo_point*数据类型。

### 定义Repository

- 推荐继承ElasticsearchRepository类。ElasticsearchRepository包含了常见的curd,page，sort接口，并支持类似jpa的扩展接口。需要符合方法命名规范。[命名规范参考](https://docs.spring.io/spring-data/elasticsearch/docs/current/reference/html/#elasticsearch.query-methods)

  ![image-20220507110918976](C:\Users\SC\AppData\Roaming\Typora\typora-user-images\image-20220507110918976.png)

```java
@Repository
public interface MovieRepository extends ElasticsearchRepository<Movie, String> {

    List<Movie> findByYear(Long year);

    Page<Movie> findByYear(Long year,Pageable pageable);

}
```

### 调用对应Repository接口查询

```

    @PostMapping("/queryMovieList")
    public R queryMovieList(){
        List<Movie> movieList = movieRepository.findByYear(2019L);
        return R.success(movieList);
    }

    @PostMapping("/queryMoviePage")
    public R queryMoviePage(){
        //demo中省略了service层
        NativeSearchQueryBuilder nativeSearchQueryBuilder = new NativeSearchQueryBuilder();
        /**
         * {
         *   "query": {
         *     "bool": {
         *       "must": [
         *         {
         *           "match": {
         *             "year": "2019"
         *           }
         *         },
         *         {
         *           "range": {
         *             "logTime": {
         *               "gte": "2022-05-07 04:00:00",
         *               "lte": "2023-01-01 04:59:59",
         *               "format": "yyyy-MM-dd HH:mm:ss"
         *             }
         *           }
         *         }
         *       ]
         *     }
         *   },
         *   "from": 0,
         *   "size": 20
         * }
         */
        nativeSearchQueryBuilder.withQuery(QueryBuilders.boolQuery()
                .must(QueryBuilders.matchQuery("year", 2019))
                .must(QueryBuilders.matchQuery("genre","动作"))
                .must(QueryBuilders.rangeQuery("logTime").gte("2022-05-07 15:55:00").lte("2022-06-17 00:00:00").format("yyyy-MM-dd HH:mm:ss").timeZone("Asia/Shanghai"))
        );
        //排序
        SortOrder sortOrder =  SortOrder.DESC ;
        nativeSearchQueryBuilder.withSorts(SortBuilders.fieldSort("logTime").order(sortOrder));
        nativeSearchQueryBuilder.withPageable(PageRequest.of(0, 20));
        return R.success(elasticsearchOperations.search(nativeSearchQueryBuilder.build(),Movie.class));
    }
    @PostMapping("/saveMovie")
    public R saveMovie(){
        Movie movie = new Movie()
                .setYear(2019L)
                .setTitle("测试数据电影")
                .setGenre(Arrays.asList("动作", "科幻"))
                .setLogTime(DateUtil.date());
        return R.success(movieRepository.save(movie));
    }
```

## 注意点

### 版本问题

Spring-Data-ElasticSearch 基于RestHighLevelClient进行封装，但是由于ES各个版本之间变化很大，因此在使用Spring-Data-ElasticSearch要注意版本问题，是否支持对应的ES版本。目前公司[96.162](http://172.16.96.162:8003/)版本为6.8.12，需要的springboot版本为2.2以上

|                  Spring Data Release Train                   |                  Spring Data Elasticsearch                   | Elasticsearch |                       Spring Framework                       |                         Spring Boot                          |
| :----------------------------------------------------------: | :----------------------------------------------------------: | :-----------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
| 2021.1 (Q)[[1](https://docs.spring.io/spring-data/elasticsearch/docs/current/reference/html/#_footnotedef_1)] | 4.3.x[[1](https://docs.spring.io/spring-data/elasticsearch/docs/current/reference/html/#_footnotedef_1)] |    7.15.2     | 5.3.x[[1](https://docs.spring.io/spring-data/elasticsearch/docs/current/reference/html/#_footnotedef_1)] | 2.5 .x[[1](https://docs.spring.io/spring-data/elasticsearch/docs/current/reference/html/#_footnotedef_1)] |
|                       2021.0 (Pascal)                        |                            4.2.x                             |    7.12.0     |                            5.3.x                             |                            2.5.x                             |
|                       2020.0 (Ockham)                        |                            4.1.x                             |     7.9.3     |                            5.3.2                             |                            2.4.x                             |
|                           Neumann                            |                            4.0.x                             |     7.6.2     |                            5.2.12                            |                            2.3.x                             |
|                          **Moore**                           |                          **3.2.x**                           |  **6.8.12**   |                          **5.2.12**                          |                         **2.2.xxv**                          |
| Lovelace[[2](https://docs.spring.io/spring-data/elasticsearch/docs/current/reference/html/#_footnotedef_2)] | 3.1.x[[2](https://docs.spring.io/spring-data/elasticsearch/docs/current/reference/html/#_footnotedef_2)] |     6.2.2     |                            5.1.19                            |                            2.1.x                             |
| Kay[[2](https://docs.spring.io/spring-data/elasticsearch/docs/current/reference/html/#_footnotedef_2)] | 3.0.x[[2](https://docs.spring.io/spring-data/elasticsearch/docs/current/reference/html/#_footnotedef_2)] |     5.5.0     |                            5.0.13                            |                            2.0.x                             |
| Ingalls[[2](https://docs.spring.io/spring-data/elasticsearch/docs/current/reference/html/#_footnotedef_2)] | 2.1.x[[2](https://docs.spring.io/spring-data/elasticsearch/docs/current/reference/html/#_footnotedef_2)] |     2.4.0     |                            4.3.25                            |                            1.5.x                             |

但是在2.2中并非使用AbstractElasticsearchConfiguration配置，而是在properties中配置以下内容

```
#elastic
#spring.data.elasticsearch.client.reactive.endpoint=172.16.96.162:8009
spring.data.elasticsearch.cluster-nodes =172.16.96.162:8009
spring.data.elasticsearch.cluster-name =CollectorDBCluster
spring.elasticsearch.rest.uris= 172.16.96.162:8003
#management.security.health.elasticsearch.enabled=false
```

## 项目报错

**现象**

启动时报错

### availableProcessors is already set to [4], rejecting [4]

**原因**

SpringBoot的netty和elasticsearch的netty相关jar冲突

**解决办法**

在启动类添加:

```
System.setProperty("es.set.netty.runtime.available.processors", "false");
```

**现象**

启动时报错

### node {#transport#-1}{HB-SrQhDQvCF5xjLR7oueg}{172.16.96.162}{172.16.96.162:8009} not part of the cluster Cluster [elasticsearch], ignoring...

**原因**

162上的elasticsearch  集群名并非默认的elasticsearch   而是 **CollectorDBCluster** （访问9200端口可查看信息）

**解决办法**

添加配置

```
spring.data.elasticsearch.cluster-name =CollectorDBCluster
```

**现象**

Elasticsearch health check failed

```
Timeout connecting to [localhost/127.0.0.1:9200]
```



**原因**

### Elasticsearch health check 连接的还是 127.0.0.1：9200

**解决办法**

添加配置（设置url 或者关闭健康检查）

```
spring.elasticsearch.rest.uris= 172.16.96.162:8003
#management.security.health.elasticsearch.enabled=false
```