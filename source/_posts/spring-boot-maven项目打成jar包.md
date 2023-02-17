---
title: spring boot maven项目打成jar包
date: 2022-11-18 17:31:23 
tags: 
	- 项目部署
categories: 运维
cover: false
---
千万不要用Intellij idea来打包项目为Jar，你应该来到项目的根目录，然后用  mvn clean package 这个命令来打包

然后在target目录下，可以得到jar文件

在target目录中通过cmd进入命令窗口，然后执行java -jar XXX.jar 命令即可启动项目

