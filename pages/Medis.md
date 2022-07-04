- [github](https://github.com/asyncins/medis)
- 简介
  collapsed:: true
	- Medis 是薄雾算法 Mist 的工程实践，其名取自 Mist 和 Redis
	- 薄雾算法是一款性能强到令作者惊喜的全局唯一ID算法，作者将它与业内同样高性能的Redis和Golang结合到一起，碰撞出了 TPS 为 2.5w/sec 这样超高性能的工程。
	- 有了 Mist 和 Medis，你就拥有了和美团 Leaf、微信 Seqsvr、百度 UIDGenerator 性能相当（甚至超过）的全局唯一 ID 服务了。相比复杂的 UIDGenerator 双 Buffer 优化和 Leaf-Snowflake，薄雾算法 Mist 简单太多了。
	- Medis 的高性能从何而来？
	- 作为开发者，你一定想知道 Medis 这 2.5w/sec 的 TPS 到底从何而来。实际上这不仅是薄雾算法本身超高性能带来的结果，作者在设计上也做了很多尝试，例如：
	- 使用 Channel 作为数据缓存，这个操作使得发号服务性能提升了 7 倍；
	  采用预存预取的策略保证 Channel 在大多数情况下都有值，从而能够迅速响应客户端发来的请求；
	  用 Gorouting 去执行耗费时间的预存预取操作，不会影响对客户端请求的响应；
	  采用 Lrange Ltrim 组合从 Redis 中批量取值，这比循环单次读取或者管道批量读取的效率更高；
	- 写入 Redis 时采用管道批量写入，效率比循环单次写入更高；
	  Seqence 值的计算在预存前进行，这样就不会耽误对客户端请求的响应，虽然薄雾算法的性能是纳秒级别，但并发高的时候也造成一些性能损耗，放在预存时计算显然更香；
	  得益于 Golang Echo 框架和 Golang 本身的高性能，整套流程下来令人很满意，如果要追求极致性能，可以试试 Rust；