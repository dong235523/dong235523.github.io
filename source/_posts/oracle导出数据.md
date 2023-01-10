---
title: oracle导出数据
date: 2023-01-10 11:17:51
tags:
  - Oracle
categories: 
  - 数据库
cover: false
---
+ ## 创建表空间
	{% asset_img 1.jpg %}
    + ### 使用Enterprise Manager Console
	{% asset_img 2.jpg %}
    + ### 账号密码都是sys，连接身份选择sysdba
    + ### 创建表空间，选项都是默认即可
    {% asset_img 3.jpg %}
    + ### 右键创建好的表空间，点击 添加数据文件
    {% asset_img 4.jpg %}
    + ### 这个一般信息不用管
    {% asset_img 5.jpg %}
    + ### 存储配置含义，数据存满后每次扩展3M，上限3个G    
+ ## 创建用户
	{% asset_img 6.jpg %}
	{% asset_img 7.jpg %}
	+ ### 一般信息页面中输入用户名密码，选择表空间
	{% asset_img 8.jpg %}
	+ ### 角色中添加DBA，勾选上管理选项，点击创建即可
+ ## 导出数据
	+ ### 先导出一个建表语句sql
	{% asset_img 9.jpg %}
	+ ### 导出数据最好用plsql的PDE格式，兼容性比较好
	+ ### Tools ->Export Tables
	{% asset_img 10.jpg %}
	+ ### 然后务必记住还要导出数据库的序列等等
	+ ### Tools ->Export User Objects 
	{% asset_img 11.jpg %}
	+ ### 把一些序列、视图等信息导出。