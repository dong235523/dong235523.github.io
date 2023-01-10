---
title: 组织机构表查询各层级level
date: 2023-01-10 11:11:51
tags:
  - Oracle
categories: 数据库
cover: false
---
## 通过表中的parentid关联的上下级数据，都可以用这种方法来查出当各个机构的层级
``` code
SELECT LTRIM(SYS_CONNECT_BY_PATH(sys_dept.dept_name, '->'), '->') AS DEPT,
       sys_dept.dept_id,
       sys_dept.dept_name,
       sys_dept.parent_id,
       LEVEL
  FROM sys_dept
 START WITH parent_id = 0
connect BY NOCYCLE PRIOR sys_dept.dept_id = sys_dept.parent_id
```
效果展示
{% asset_img 1.jpg %}
