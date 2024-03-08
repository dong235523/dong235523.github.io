---
title: oracle客户端client10g安装及配置
date: 2024-03-08 10:03:37
tags:
  - Oracle
categories: 
  - 数据库
cover: false
---

client10g是oracle的客户端，记录一下安装方法及需要的一些配置

打开安装程序
{% asset_img 1.jpg %}

然后下一步
{% asset_img 2.jpg %}

接下来注意，必须要选择管理员
{% asset_img 3.jpg %}

然后一直下一步就可以了
{% asset_img 4.jpg %}

最后一步可以勾选常规配置

接下来安装plsql
正常安装即可

安装完plsql后，将tnsnames.ora文件复制到D:\DevelopmentTool\oracle_client\NETWORK\ADMIN文件夹下
[点击下载](tnsnames.ora)
注：tnsnames.ora配置的是oracle连接的数据源，示例文件中有单数据源和双数据源两种配置

在我本机装完plsql后，直接打开会出现空白界面，必须右键以管理员身份打开才行

接下来连接数据库时候出现了 提示ORA-12638:身份证明检索失败
这是由于Oracle的认证方式使用了本地操作系统认证（NTS）导致的。只需通过配置文件修改为口令文件认证（NONE）或者直接通过Oracle自带的Net Manager修改
通过配置文件修改为口令文件认证（NONE）即可

在D:\DevelopmentTool\oracle_client\NETWORK\ADMIN文件夹中找到sqlnet.ora文件
将SQLNET.AUTHENTICATION_SERVICES= (NTS)中NTS修改为NONE。如果没有这句，手动添加一下即可。
{% asset_img 5.jpg %}
修改完成后，需要重启PLSQL生效。