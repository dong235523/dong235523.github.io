---
title: ORACLE根据任意id查询所有父级数据及子级数据
date: 2023-03-13 16:05:27
tags:
  - Oracle
categories: 
  - 数据库
cover: false
---
在工作中碰到一个需求，ztree控件过滤跟当前用户部门无关的其他部门，并且当前用户可能横跨多个部门

#### 假设有一张省市区表

### 根据id查询所有的子数据
	需求1：我输入山东省的id,会把山东省及下面的市区都查询出来
```sql
select * from REGION 
start with id = 2
connect by prior id = pid -- prior 右边是子级id，就往子级的方向查询
ORDER BY id;
```
	结果：
{% asset_img 1.png %}

### 根据id查询所有的父数据
	需求2：我输入黄岛区的id,会把黄岛区及其所在的市省国查询出来
```sql
select * from REGION 
start with id = 8
connect by prior pid = id -- prior 右边是父id，就往父级的方向查询
ORDER BY id;
```
	结果：
{% asset_img 2.png %}

### 通过对这两种方法进行结合，完成我需要的数据过滤
```sql
select distinct d.dept_id,d.parent_id,d.dept_name,d.order_num
  from (select *
          from sys_dept t
         start with t.dept_id in ('310','301')
        connect by prior dept_id = parent_id -- prior 右边是子级id，就往子级的方向查询
        union all
        select *
          from sys_dept t
         start with t.dept_id in ('310','301')
        connect by prior t.parent_id = dept_id) d -- prior 右边是父id，就往父级的方向查询
 where del_flag = 0
 order by parent_id, order_num
```
	结果：
{% asset_img 3.png %}
	注：连子集和父集都包含了当前部门数据，还有同时查几个部门，父集子集都会重复几次
	所以需要distinct