---
title: mysql实现ORACLE的partition by功能
date: 2023-02-03 16:41:44
tags:
  - mysql
categories: 数据库
cover: false
---
## oracle的partition by是一个非常方便的数据排序过滤语法，但是mysql不支持。所以通过group_concat来模拟一下
```
SELECT t.imei,lon,lat,id,substring_index( group_concat( t.create_time ORDER BY t.create_time DESC ), ',', 1 ) createTime
FROM (
SELECT *
FROM equip_bracelet_event ord 
WHERE
	ord.lon is not null and ord.lat is not null
	and ord.lon !=0 and ord.lat !=0
	AND create_time > 0 
ORDER BY create_time DESC 
	) t 
GROUP BY imei
```