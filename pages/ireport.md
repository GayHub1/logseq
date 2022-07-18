- 简介
  collapsed:: true
	- iReport 是为JasperReports Library和JasperReports Server设计的报表可视化设计器。
	- iReport是一个能够创建复杂报表的开源项目。它100%使用Java语言编写。是目前全球最为流行的开源报表设计器。
	- 由于它丰富的图形界面，你能够很快的创建出任何一种你想要的报表。
	- 用于打印的数据可以通过多种方式获取包括：JDBC, TableModels, JavaBeans, XML,Hibernate(支持HQL查询语言), CSV等。
	- 它支持多种输出格式包括：PDF,RTF,XML,XLS,CSV,HTM。
- 安装
  collapsed:: true
	- > 需要环境 [JDK1.7 ](https://www.oracle.com/java/technologies/javase/javase7-archive-downloads.html) ，并修改JAVA_HOME系统变量
	- [5.6下载地址](https://sourceforge.net/projects/ireport/files/iReport/iReport-5.6.0/iReport-5.6.0.zip/download)
- 平时一些问题
  collapsed:: true
	- ### 给某一字段加下划线或横线
	  collapsed:: true
		- 在字段$F{sdaddress}的properties设置的Font标签下，把Underline前的复选框选上，这就类似于word中个文字加下划线。
	- 报错定位
		- 组件单独粘到一个尝试能否编译