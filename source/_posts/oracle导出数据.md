---
title: oracle导出数据
date: 2023-03-03 14:29:34
tags:
  - Oracle
categories: 
  - 数据库
cover: false
---
### 导出表结构
	先导出一个建表语句sql
	Tools ->Export User Objects 
{% asset_img 11.jpg %}
	可以把所有表结构及序列、视图等信息导出。

### 导出数据有两种方式：
	1、plsql的PDE格式，兼容性比较好

	2、用CMD命令导出dmp格式数据
```java
exp dp/dp@orcl file=d:\dp.dmp
```
	解析：exp 数据库账号/数据库密码@sid file=导出文件路径
	
	PS：在服务器上导出的dmp只能用服务器端来导入，但是客户端导出的dmp文件既可以
	用服务器端导入，也可以用客户端导入