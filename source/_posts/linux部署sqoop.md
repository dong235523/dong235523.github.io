---
title: linux部署sqoop
date: 2023-06-13 13:45:17
tags: 
  - linux
categories: 运维
cover: false
---

## sqoop简介
<font size=5>
Sqoop(发音：skup)是一款开源的工具，主要用于在Hadoop(Hive)与传统的数据库(mysql、postgresql…)间进行数据的传递，可以将一个关系型数据库（例如 ： MySQL ,Oracle ,Postgres等）中的数据导进到Hadoop的HDFS中，也可以将HDFS的数据导进到关系型数据库中。
Sqoop项目开始于2009年，最早是作为Hadoop的一个第三方模块存在，后来为了让使用者能够快速部署，也为了让开发人员能够更快速的迭代开发，Sqoop独立成为一个Apache项目。
Sqoop是一个在（MySQL、Oracle）等关系型数据库和大数据平台之间进行批量数据迁移的工具。Sqoop底层能实现将关系型数据库中的数据迁移到大数据平台上是因为sqoop的底层是采用MapReduce程序实现抽取、转换、加载，MapReduce本身就并行化和高容错率，能很好的保证数据的迁移，而且与Kettle等传统ETL工具相比，Sqoop的任务跑在Hadoop集群上，能有效减少ETL服务器资源的使用情况<br />
</font>


## sqoop安装


+ ### 一、安装包的获取：
	+ ### sqoop安装包：
	官网：http://archive.apache.org/dist/sqoop/1.4.6/sqoop-1.4.6.bin__hadoop-2.0.4-alpha.tar.gz
	+ ### mysql驱动：
	[点击下载](mysql-connector-java-8.0.30.jar "jar包")

+ ## 二、上传安装包到服务器
	+ ### 将sqoop安装包上传到服务器上

+ ## 三、进行安装配置
	+ ### 解压sqoop压缩包
```
tar -zvxf sqoop-1.4.6.bin__hadoop-2.0.4-alpha.tar.gz
```
	+ ### 修改名字
```
mv sqoop-1.4.6.bin__hadoop-2.0.4-alpha sqoop
```
	+ ### 修改配置文件
	+ ### 在sqoop/conf/目录下修改sqoop-env-template.sh的名字
```
mv sqoop-env-template.sh sqoop-env.sh
```
	+ ### 向sqoop-env.sh中最后添加如下配置：
```
export HADOOP_COMMON_HOME=/opt/module/hadoop-2.7.2
export HADOOP_MAPRED_HOME=/opt/module/hadoop-2.7.2
export HIVE_HOME=/opt/module/hive
export ZOOKEEPER_HOME=/opt/module/zookeeper-3.4.10
export ZOOCFGDIR=/opt/module/zookeeper-3.4.10/conf
export HBASE_HOME=/opt/module/hbase
```
注：我的服务器没有装hbase和zookeeper，所以后边三个我没有配，暂时可以正常启动，以后要是发现有什么问题再进行补充

	+ ### 将jdbc驱动拷贝到sqoop/lib/目录下

+ ## 四、验证sqoop
	+ ### 使用一个command命令检查sqoop是否安装完成
```
bin/sqoop help
```
	+ ### 出现以下内容便是安装完成：
{% asset_img 1.jpg %}
	+ ### 测试是否能连接上mysql数据库
```
bin/sqoop list-databases --connect jdbc:mysql://192.168.2.140:3306/ --username root --password 123456
```
	+ ### 如果连接成功会获取到当前mysql数据库中的数据库
{% asset_img 2.jpg %}