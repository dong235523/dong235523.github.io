---
title: mysql快捷生成数据库结构设计
date: 2024-05-18 11:46:45
tags:
  - mysql
categories: 
  - 数据库
cover: false
---

公司项目需要写数据库设计文档，就是数据库字段信息写到word中，挨个字段的粘贴效率太低了
所以需要进行一些快捷操作
下边以mysql数据库，Navicat软件举例

+ ### 在Navicat中整理表信息
```
select @rank:=@rank + 1 AS 序号,
	b.COLUMN_KEY 主键,
    b.COLUMN_NAME 名称,
	b.column_type 字段类型,
	case IS_NULLABLE when 'NO' then ''
	else '√' end  是否可空,
	b.COLUMN_DEFAULT 默认值,
	b.column_comment 字段说明,
	'' 备注
from information_schema.COLUMNS b
, (SELECT @rank:= 0) c
where b.TABLE_SCHEMA = 'hk_iot'
and b.TABLE_NAME = 'thing_model_argument_data'
```
查询结果：
{% asset_img 1.png %}
注：TABLE_SCHEMA（数据库名）  TABLE_NAME（表名）

+ ### 将查询结果导出成excel
{% asset_img 2.jpg %}
{% asset_img 3.jpg %}

复制excel内容到目标word中
{% asset_img 4.jpg %}

+ ### 格式化表格
粘过去之后是这样无样式的表格
{% asset_img 5.png %}
选择边框和底纹
{% asset_img 6.jpg %}
{% asset_img 7.jpg %}
这样效果就差不多了
{% asset_img 8.jpg %}
然后从示例的表格中用格式刷刷点样式下来
{% asset_img 9.jpg %}

最后在第一格上方插入一行，把表头复制进来
{% asset_img 10.jpg %}
这样一个表就完事了
{% asset_img 11.jpg %}

虽然这个流程也挺繁琐的，但是总比挨个字段复制要快，等以后有更快的方法在更新。