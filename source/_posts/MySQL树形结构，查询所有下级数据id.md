---
title: MySQL树形结构，查询所有下级数据id
date: 2023-12-06 14:44:01
tags:
  - mysql
categories: 
  - 数据库
cover: false
---

树形结构常用于菜单或者组织机构，查询所有下级在做数据权限方面非常重要

下面以若依的菜单表为例子，写一个查询语句
```
SELECT menu_id FROM(
	SELECT t1.menu_id,t1.parent_id,
	IF
		( find_in_set( parent_id, @pids ) > 0, @pids := concat( @pids, ',', menu_id ), 0 ) AS isbig 
	FROM
		( SELECT menu_id, parent_id FROM sys_menu t ORDER BY menu_id, parent_id ) t1,
		( SELECT @pids := 108 ) t2 
		) t3 
WHERE
	isbig > 0;
```

语法解析：
menu_id：查询的子集ID
parent_id：查询的父级ID

<span>
	@pids := 变量定义。<br/>
	concat()  拼接字符串
</span>
```
	find_in_set(parent_id , @pids)  parent_id  数据是否在@pids字符串中。
	　　select find_in_set('2','1,2');返回2
	　　select find_in_set('6','1');返回0
	　　左边的是否包含在右边。
```
<span>
	if（express1,express2,express3）条件语句，if语句类似三目运算符，当exprss1成立时，执行express2,否则执行express3;
	t1的为父子级排序，t2为父级ID条件查询，t3条件查询父级的是否存在子集。
</span>