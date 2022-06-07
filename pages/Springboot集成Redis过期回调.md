- 一、修改配置文件
  windows系统在redis.windows.conf中找到 notify-keyspace-events 改为notify-keyspace-events EX 并取消注解/font>
- 字符	发送的通知
  K	键空间通知，所有通知以__keyspace@<db>__ 为前缀
  E	键事件通知，所有通知以 __keyevent@<db>__ 为前缀
  g	DEL 、 EXPIRE 、 RENAME 等类型无关的通用命令的通知
  $	字符串命令的通知
  l	列表命令的通知
  s	集合命令的通知
  h	哈希命令的通知
  z	有序集合命令的通知
  x	过期事件，每当有过期键被删除时发送
  e	驱逐事件，每当有键因为maxmemory政策而被删除时发送
  A	参数 g$lshzxe 的别名
  二、springboot实现：
  （1）配置pom.xml
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
  1
  2
  3
  4
  5
  6
  7
  8
  9
  10
  11
  12
  13
  14
  （2）配置properties文件
- server.port=8222
- server.servlet.context-path=/
- spring.redis.host=127.0.0.1
  spring.redis.port=6379
  spring.redis.database= 0
  spring.redis.timeout=1800000
  spring.redis.lettuce.pool.max-active=20
  spring.redis.lettuce.pool.max-wait=-1
  #最大阻塞等待时间(负数表示没限制)
  spring.redis.lettuce.pool.max-idle=5
  spring.redis.lettuce.pool.min-idle=0
  1
  2
  3
  4
  5
  6
  7
  8
  9
  10
  11
  12
  13
  （3）创建配置类
- @Configuration
  public class RidesConfig {
- @Autowired
    private RedisConnectionFactory redisConnectionFactory;//redis连接池
- @Bean
    public ChannelTopic expiredTopic() {
        return new ChannelTopic("__keyevent@0__:expired");  // 选择0号数据库
    }
- @Bean
    public RedisMessageListenerContainer redisMessageListenerContainer() {
        RedisMessageListenerContainer redisMessageListenerContainer = new RedisMessageListenerContainer();
        redisMessageListenerContainer.setConnectionFactory(redisConnectionFactory);
        return redisMessageListenerContainer;
    }
  }
- 1
  2
  3
  4
  5
  6
  7
  8
  9
  10
  11
  12
  13
  14
  15
  16
  17
  18
  19
  (4)创建订阅方
- @Component
  public class RedisSubService extends KeyExpirationEventMessageListener {
- @Autowired
    private RedisTemplate<String,String> redisTemplate;
- public RedisSubService(RedisMessageListenerContainer listenerContainer) {
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
- }
  1
  2
  3
  4
  5
  6
  7
  8
  9
  10
  11
  12
  13
  14
  15
  16
  17
  18
  19
  20
  （5）创建消息发送方
- @RestController
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
  ————————————————
  版权声明：本文为CSDN博主「申 月」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
  原文链接：https://blog.csdn.net/meat_eating/article/details/118239986