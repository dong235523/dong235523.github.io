---
title: sequence的用法
date: 2023-01-10 09:46:32
tags:
  - Oracle
categories: 数据库
cover: false
---
### oracle因为没有自增序列，所以可以用sequence序列加触发器来进行模拟功能。

1. 创建sequence
``` code
create sequence SEQ_test_sequence_ID  --创建一个名叫SEQ_test_sequence_ID 的序列

minvalue 1  --最小值是1

maxvalue 99999999  --最大值是99999999  

start with 1  --从1开始

increment by 1  --每次增长1

nocache

order;
``` 

2. 创建触发器
``` code
CREATE OR REPLACE TRIGGER tg_on_id_chance_contract  --创建一个名叫tg_on_id_chance_contract  的触发器
       BEFORE INSERT ON test_sequence FOR EACH ROW WHEN (new.ID is null)  --test_sequence（使用触发器的表名） new.ID（ID是表test_sequence的主键）
BEGIN
       SELECT SEQ_TEST_SEQUENCE_ID.nextval into:new.ID from dual;  --从SEQ_TEST_SEQUENCE_ID序列中获取数据当做new.ID 的值
END;
``` 

这样数据插入时就不需要给id字段赋值了