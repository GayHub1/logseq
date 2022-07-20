- 微服务的架构是一种架构风格，而SpringCloud是实现微服务架构的有序集合。
- 当前服务注册与发现的主流技术
	- id:: 62d7f592-9b83-43ac-91fa-83ac0ad07d27
	  | 对比项                | euerka                        | Consul                  | zookeeper           | etcd              |
	  | --------------------- | ----------------------------- | ----------------------- | ------------------- | ----------------- |
	  | 服务健康检查          | 可配支持                      | 服务状态，内 存，硬盘等 | (弱)长连接keepalive | 连接心跳          |
	  | 多数据中心            |                               | 支持                    |                     |                   |
	  | kv 存储服务           |                               | 支持                    | 支持                | 支持              |
	  | 一致性                |                               | raft                    | paxos               | raft              |
	  | cap                   | ap                            | cp                      | cp                  | cp                |
	  | 使用接口(多语 言能力) | http                          | 支持 http 和 dns        | 客户端              | http/grpc         |
	  | watch 支持            | 支持 long polling/大 部分增量 | 全量/支持long polling   | 支持                | 支持 long polling |
	  | 自身监控              | metrics                       | metrics                 |                     | metrics           |
	  | 安全                  |                               | acl /https              | acl                 | https 支持 （弱） |
	  | spring cloud 集成     | 支持                          | 支持                    | 支持                | 支持              |
-