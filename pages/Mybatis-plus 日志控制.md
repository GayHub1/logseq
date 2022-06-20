## 开启日志

yml中配置中添加打印SQL配置：

在 **application.yml** 中 **mybatis-plus**下的 ** configuration:**下添加一行打印sql配置
```
  log-impl: org.apache.ibatis.logging.stdout.StdOutImpl

```
添加mybatis-plus后整体配置为
```
mybatis-plus:
type-aliases-package: com.sc.iot
type-aliases-super-type: java.lang.Object
configuration:
  log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
  map-underscore-to-camel-case: true
  cache-enabled: true
  lazy-loading-enabled: true
  multiple-result-sets-enabled: true
  use-generated-keys: true
  default-statement-timeout: 60
  default-fetch-size: 100
```
# 日志生成路径

```
logging.path=/Users/jackie/workspace/rome/ 
logging.file=springbootdemo.log
```
- logging.path
  
  该属性用来配置日志文件的路径
- logging.file
  
  该属性用来配置日志文件名，如果该属性不配置，默认文件名为spring.log
  重启项目后可以就可以看见 log文件了
  ![](http://qcyd0eqp3.bkt.clouddn.com/5f042b8a3ca27.png)