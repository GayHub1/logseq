- GroupingBy收集器
	- 根据单一字段分组
	  collapsed:: true
		- 最简单的groupingBy方法，只有一个分类函数做参数。分类函数作用于strema里面的每个元素。分类函数处理后返回的每个元素作为返回Map的key。
		  根据博客文章类型来分组：
		- ```JAVA
		  Map<BlogPostType, List<BlogPost>> postsPerType = posts.stream()
		  .collect(groupingBy(BlogPost::getType));
		  ```
	- 根据Map的key的类型分组
		- 分类函数并没有限制返回字符串或标量值。返回map的key可以是任何对象。只要实现了其equals和hashcode方法。
		  下面示例根据type和author组合而成的BlogPost实例来分组：
		- ```Java
		   Map<BlogPost, List<BlogPost>> postsPerTypeAndAuthor = posts.stream().collect(groupingBy(post -> new BlogPost()));
		  ```
-
-