- 简介
	- OpenFeign是Spring Cloud 在Feign的基础上支持了Spring MVC的注解，如@RequesMapping等，是一
	  id:: 62d81408-9b45-42a5-a241-d9f5549d6b2d
	  个轻量级的Http封装工具对象,大大简化了Http请求，使得我们对服务的调用转换成了对本地接口方法的
	  调用。
	- OpenFeign 的 @FeignClient 可以解析SpringMVC的 @RequestMapping 注解下的接口，并通过动态代
	  理的方式产生实现类，实现类中做负载均衡并调用其他服务。
		- 集成了Ribbon的负载均衡功能
		- 集成Hystrix的熔断器功能
		- 支持请求压缩
		- 大大简化了远程调用的代码，同时功能还增强啦
- 前身
  collapsed:: true
	- Feign
		- Feign是Spring Cloud组件中的一个轻量级RESTful的HTTP服务客户端。
		- Feign内置了Ribbon，用来做客户端负载均衡，去调用服务注册中心的服务。
		- Feign的使用方式是：使用Feign的注解定义接口，调用这个接口，就可以调用服务注册中心的服务。
		- Feign支持的注解和用法请参考官方文档：https://github.com/OpenFeign/feign
		- Feign本身不支持Spring MVC的注解，它有一套自己的注解。
-