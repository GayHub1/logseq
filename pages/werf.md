- [官网](https://werf.io/)
- [github](https://github.com/werf/werf)
- ![Replaced by Image Uploder](https://cdn.jsdelivr.net/gh/GayHub1/images@master/img/image_1656771663399_0.png)
- 简介
	- werf是一种用于向Kubernetes实施高效且一致的软件交付的解决方案。
	  它涵盖了CI/CD和相关工件的整个生命周期，并结合一些常用工具（Git、Docker、Helm、K8s）和最佳实践。
	- werf使用Dockerfiles或基于自定义语法的可替代的快速内置构建器来构建Docker映像。 它还会从容器注册表中删除未使用的映像。
	- werf使用Helm兼容格式的图表将应用程序部署到Kubernetes，同时有方便的自定义和改进的推出跟踪机制、错误检测和日志输出。
	- werf不是一个完整的CI/CD解决方案，而是一个用于创建可以嵌入到任何现有CI/CD系统中的管道的工具。它实际上一个是“连接点”，以将这些实践带入到应用程序。