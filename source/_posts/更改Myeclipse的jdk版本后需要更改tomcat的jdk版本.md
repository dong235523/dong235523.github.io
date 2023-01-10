---
title: 更改Myeclipse的jdk版本后需要更改tomcat的jdk版本
date: 2018-08-17 15:02:17
tags: 
  - myEclipse
categories: 开发工具配置
cover: false
---
<font size=4>
今天将Myeclipse的jdk版本从1.7升级到了1.8，然后启动tomcat项目就开始报错，找了好久发现原因是Myeclipse中的tomcat服务没有跟着升级jdk，现在记录一下，以备以后查看

右键Myeclipse中Servers的Tomcat 8.x，在弹出界面选择Configure Server Connector，

然后MyEclipse--Servers--Tomcat--Tomcat 8.x--JDK，在这个界面升级tomcat的jdk版本。
</font>
