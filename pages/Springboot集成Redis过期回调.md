- #[[excerpt]] [(109 条消息) springboot 集成 redis 完成过期回调_申 月的博客 - CSDN 博客_springbootredis 回调](https://blog.csdn.net/meat_eating/article/details/118239986)
- tags: #[[SimpRead]]
- read date: [[2022_06_07  ]]
- desc: 目录标题一、修改配置文件二、springboot实现：三、测试：一、修改配置文件windows系统在redis.windows.conf中找到 notify-keyspace-events 改为notify-keyspace-events EX 并取消注解/font&gt;字符发送的通知K键空间通知，所有通知以__keyspace@&lt;db&gt;__ 为前缀E键事件通知，所有通知以 __keyevent@&lt;db&gt;__ 为前缀gDEL 、 EXPIRE 、 RENAME 等类型无关的通
- 一、修改配置文件
  ========
  
  windows 系统在 redis.windows.conf 中找到 notify-keyspace-events 改为 notify-keyspace-events EX 并取消注解 / font>
  
  <table><thead><tr><th>字符</th><th>发送的通知</th></tr></thead><tbody><tr><td>K</td><td>键空间通知，所有通知以<code>__keyspace@&lt;db&gt;__</code> 为前缀</td></tr><tr><td>E</td><td>键事件通知，所有通知以 <code>__keyevent@&lt;db&gt;__</code> 为前缀</td></tr><tr><td>g</td><td><code>DEL</code> 、 <code>EXPIRE</code> 、 <code>RENAME</code> 等类型无关的通用命令的通知</td></tr><tr><td>$</td><td>字符串命令的通知</td></tr><tr><td>l</td><td>列表命令的通知</td></tr><tr><td>s</td><td>集合命令的通知</td></tr><tr><td>h</td><td>哈希命令的通知</td></tr><tr><td>z</td><td>有序集合命令的通知</td></tr><tr><td>x</td><td>过期事件，每当有过期键被删除时发送</td></tr><tr><td>e</td><td>驱逐事件，每当有键因为 maxmemory 政策而被删除时发送</td></tr><tr><td>A</td><td>参数 <code>g$lshzxe</code> 的别名</td></tr></tbody></table>
  
  二、springboot 实现：
  ================
  
  （1）配置 pom.xml
  
  ```
  <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
            <version>2.1.3.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>redis.clients</groupId>
            <artifactId>jedis</artifactId>
            <version>3.6.0</version>
        </dependency>
        <dependency>
            <groupId>org.apache.commons</groupId>
            <artifactId>commons-pool2</artifactId>
         </dependency>
  ```
  
  （2）配置 properties 文件
  
  ```
  server.port=8222
  
  server.servlet.context-path=/
  
  spring.redis.host=127.0.0.1
  spring.redis.port=6379
  spring.redis.database= 0
  spring.redis.timeout=1800000
  spring.redis.lettuce.pool.max-active=20
  spring.redis.lettuce.pool.max-wait=-1
  #最大阻塞等待时间(负数表示没限制)
  spring.redis.lettuce.pool.max-idle=5
  spring.redis.lettuce.pool.min-idle=0
  ```
  
  （3）创建配置类
  
  ```
  @Configuration
  public class RidesConfig {
  
    @Autowired
    private RedisConnectionFactory redisConnectionFactory;//redis连接池
  
    @Bean
    public ChannelTopic expiredTopic() {
        return new ChannelTopic("__keyevent@0__:expired");  // 选择0号数据库
    }
  
    @Bean
    public RedisMessageListenerContainer redisMessageListenerContainer() {
        RedisMessageListenerContainer redisMessageListenerContainer = new RedisMessageListenerContainer();
        redisMessageListenerContainer.setConnectionFactory(redisConnectionFactory);
        return redisMessageListenerContainer;
    }
  }
  ```
  
  (4) 创建订阅方
  
  ```
  @Component
  public class RedisSubService extends KeyExpirationEventMessageListener {
  
    @Autowired
    private RedisTemplate<String,String> redisTemplate;
  
    public RedisSubService(RedisMessageListenerContainer listenerContainer) {
        super(listenerContainer);
    }
  	//回调到这个方法
    @Override
    public void onMessage(Message message, byte[] pattern) {
        System.out.println(new String(message.getBody()));
        System.out.println(new String(message.getChannel()));
        System.out.println(new String(pattern));
        super.onMessage(message, pattern);
    }
  
  
  }
  ```
  
  （5）创建消息发送方
  
  ```
  @RestController
  public class TestController {
    @Autowired
    private RedisTemplate<String,String> redisTemplate;
    @RequestMapping("/index")
    public String index(){
    	//我们需要将过期后要拿到的消息作为key
        redisTemplate.opsForValue().set("12465789","11111111",10, TimeUnit.SECONDS);
        return "success";
    }
  }
  ```
  
  三、测试：
  =====
  
  测试结果：  
  
  ok！
- [📌](<http://localhost:7026/reading/9?title=(109 条消息) springboot 集成 redis 完成过期回调_申 月的博客 - CSDN 博客_springbootredis 回调#id=1654572612303>)  ![](https://img-blog.csdnimg.cn/20210626091210258.png)