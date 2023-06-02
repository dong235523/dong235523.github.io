---
title: linux部署hive
date: 2023-06-02 15:17:20
tags: 
  - linux
categories: 运维
cover: false
---
环境要求：
    1、拥有一台 Centos Linux 机器
    2、 Centos Linux 安装好 JDK、配置好环境变量
    3、在 CentOS Linux 上安装好 Hadoop
    4、在 CentOS Linux 上安装好 MySQL（docker部署的mysql也可以）

下载hive
	hive 官网：
	https://hive.apache.org/

	hive 安装包官网下载地址（官网，下载速度慢）：
	https://dlcdn.apache.org/hive/hive-3.1.3/apache-hive-3.1.3-bin.tar.gz

	hive 安装包国内镜像下载链接（清华大学开源软件镜像站，下载速度快）：
	https://mirrors.tuna.tsinghua.edu.cn/apache/hive/hive-3.1.3/apache-hive-3.1.3-bin.tar.gz

上传 Hive 安装包、解压、放到安装位置
```
# 进入到root目录
cd /root
# 解压
tar -zxvf apache-hive-3.1.3-bin.tar.gz
# 创建安装目录
mkdir /usr/local/hive
# 将解压后的hive挪到创建的安装目录
mv /root/apache-hive-3.1.3-bin/ /usr/local/hive/
# 进入到安装目录
cd /usr/local/hive/apache-hive-3.1.3-bin/
# 查看
ll
```

配置 hive
	1、hive-site.xml
	在 hive 安装目录的 conf 目录下，创建 hive-site.xml 配置文件。并将以下内容写入其中。
	```
	vi /usr/local/hive/apache-hive-3.1.3-bin/conf/hive-site.xml
	```
	```
	<configuration>

	    <property>
	        <name>javax.jdo.option.ConnectionDriverName</name>
	        <!--hive 元数据库的JDBC驱动类，这里选则 MySQL-->
	        <value>com.mysql.cj.jdbc.Driver</value>
	    </property>

	    <property>
	        <name>javax.jdo.option.ConnectionURL</name>
	        <!--hive_metadata 为 hive 元数据在 MySQL 中的库名，注意字符集设置，由于是在XML中，URL中的&需要转译为&-->
	        <value>jdbc:mysql://127.0.0.1:3306/hive_metadata?createDatabaseIfNotExist=true&amp;characterEncoding=UTF-8&amp;useSSL=false</value>
	    </property>

	    <property>
	        <name>javax.jdo.option.ConnectionUserName</name>
	        <!--MySQL登录账户名-->
	        <value>root</value>
	    </property>

	    <property>
	        <name>javax.jdo.option.ConnectionPassword</name>
	        <!--MySQL登录密码-->
	        <value>123456</value>
	    </property>

	    <property>
	        <!--hive 表数据在 HDFS 的默认位置。创建内部表时，如果不指定 location，表数据则存储与该位置。-->
	        <name>hive.metastore.warehouse.dir</name>
	        <value>/user/hive/warehouse/internal</value>
	    </property>

	    <property>
	        <!--hive 外部表数据在 HDFS 的默认位置。创建外部表时，如果不指定 location，表数据则存储与该位置。-->
	        <name>hive.metastore.warehouse.external.dir</name>
	        <value>/user/hive/warehouse/external</value>
	    </property>

	    <property>
	        <name>hive.server2.enable.doAs</name>
	        <value>false</value>
	    </property>

	</configuration>
	```
	2、hive-env.sh
	将 hive 安装目录的 conf 目录下的 hive-env.sh.template 复制一份改名为 hive-env.sh 。
	```
	cp /usr/local/hive/apache-hive-3.1.3-bin/conf/hive-env.sh.template /usr/local/hive/apache-hive-3.1.3-bin/conf/hive-env.sh
	```
	对 ive-env.sh 进行修改，将 HADOOP_HOME 的注释放开，其值改为本机 Hadoop 的安装目录 /usr/local/hadoop/hadoop-3.3.4 。即：
	```
	vi /usr/local/hive/apache-hive-3.1.3-bin/conf/hive-env.sh
	```
	```
	HADOOP_HOME=/usr/local/hadoop/hadoop-3.3.4
	```

放置 MySQL jdbc jar包
	Maven 中央仓库下载地址：
	https://repo1.maven.org/maven2/mysql/mysql-connector-java/8.0.30/mysql-connector-java-8.0.30.jar
	将 mysql-connector-java-8.0.30.jar 上传到 /usr/local/hive/apache-hive-3.1.3-bin/lib 下。

设置环境变量
	```
	vi /etc/profile
	```
	在文件末尾添加以下内容：
	```
	export HIVE_HOME=/usr/local/hive/apache-hive-3.1.3-bin
	export PATH=$HIVE_HOME/bin:$PATH
	```
	使环境变量生效：
	```
	# 运行配置文件
	source /etc/profile
	# 检查 PATH 中是否包含 $HIVE_HOME/bin 对应的绝对路径
	echo $PATH
	```

初始化元数据库
	初始化元数据库，指定元数据库类型为 MySQL 。
	```
	# 查看MySQL是否启动
	service mysqld status
	# 如果没有启动，则启动MySQL
	service mysqld start
	schematool -initSchema -dbType mysql
	```
	注：schematool 命令位于 /usr/local/hive/apache-hive-3.1.3-bin/bin/ ，已经配置在环境变量path中，可以直接使用。
		docker部署的mysql有另外的命令

启动 Hadoop
	Hive 是依赖 MySQL 和 Hadoop 的，所以，先启动 MySQL、Hadoop。
	```
	# 查看Hadoop是否启动
	jps -l |grep hadoop
	# 如果没有启动，则启动Hadoop
	start-all.sh
	```

编写后台启动hive的脚本
	编写后台启动脚本：
	```
	vi /usr/local/hive/apache-hive-3.1.3-bin/bin/start-hive-metastore-hiveserver2.sh
	```
	写入以下内容：
	```
	#!/bin/bash
	nohup hive --service metastore >> /usr/local/hive/apache-hive-3.1.3-bin/log/metastore.log 2>&1 &
	nohup hive --service hiveserver2 >> /usr/local/hive/apache-hive-3.1.3-bin/log/hiveserver2.log 2>&1 &
	```
	```
	# 赋予启动脚本执行权限
	chmod +x /usr/local/hive/apache-hive-3.1.3-bin/bin/start-hive-metastore-hiveserver2.sh
	# 创建日志目录：
	mkdir /usr/local/hive/apache-hive-3.1.3-bin/log
	```

启动hive
	```
	start-hive-metastore-hiveserver2.sh
	```
	查看两个日志，无报错即可
	```
	tail -1000f /usr/local/hive/apache-hive-3.1.3-bin/log/metastore.log
	tail -1000f /usr/local/hive/apache-hive-3.1.3-bin/log/hiveserver2.log
	```