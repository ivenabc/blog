
---
title: "SQL高级特性"
date: 2019-12-09
author: "Iven"
draft: false
weight: 101
---

Postgresql sql 高级特性
<!--more-->
## 查询顺序
```text
(7)     SELECT 
(8)     DISTINCT <select_list>
(1)     FROM <left_table>
(3)     <join_type> JOIN <right_table>
(2)     ON <join_condition>
(4)     WHERE <where_condition>
(5)     GROUP BY <group_by_list>
(6)     HAVING <having_condition>
(9)     ORDER BY <order_by_condition>
(10)    LIMIT <limit_number>
```

## 视图
```text
CREATE [ OR REPLACE ] [ TEMP | TEMPORARY ] [ RECURSIVE ] VIEW name [ ( column_name [, ...] ) ]
AS query
[ WITH [ CASCADED | LOCAL ] CHECK OPTION ]
```

**CHECK OPTION**  
WITH CASCADED CHECK OPTION 和 WITH LOCAL CHECK OPTION区别是前者会检查自己得条件同时级联往下检查视图得条件是否满足，后者是只是检查自己得条件是否满足
另外CHECK OPTION不应该和RECURSIVE视图一起使用

- 对于update,有with check option，要保证update后，数据要被视图查询出来  
- 对于delete,有无with check option都一样  
- 对于insert,有with check option，和update相同 要保证insert后，数据要被视图查询出来  
- 对于没有where子句的视图，也就没有视图自己的约束，因此使用with check option是多余的  

> 当使用CREATE VIEW OR REPLACE VIEW修改某字段名、中间增加字段、删除字段时候常常报"cannot change name of view column xxx to yyy"的错误，建议先使用DROP VIEW，然后再CREATE VIEW

创建一个由数字 1 到 100 组成的递归视图：
```sql
CREATE RECURSIVE VIEW public.nums_1_100 (n) AS
    VALUES (1)
UNION ALL
    SELECT n+1 FROM nums_1_100 WHERE n < 100;
```

## WITH 
with recursive 用法,可以循环调用自己得表  
下面是展示了循环查询自己的父节点
```text
e_commerce=# create table test_area(id int, name varchar(32), fatherid int);
CREATE TABLE
e_commerce=# \d test_area 
                     Table "public.test_area"
  Column  |         Type          | Collation | Nullable | Default 
----------+-----------------------+-----------+----------+---------
 id       | integer               |           |          | 
 name     | character varying(32) |           |          | 
 fatherid | integer               |           |          | 

e_commerce=# insert into test_area values(1, '中国', 0);
INSERT 0 1
e_commerce=# insert into test_area values(2, '安徽', 1);
INSERT 0 1
e_commerce=# insert into test_area values(3, '六安', 2);
INSERT 0 1
e_commerce=# insert into test_area values(4, '顺河', 3);
INSERT 0 1

e_commerce=# with recursive r as(select * from test_area where id=4 
union all 
select test_area.* from  test_area,r where test_area.id = r.fatherid)
e_commerce-# select * from r order by id ;
 id | name | fatherid 
----+------+----------
  1 | 中国 |        0
  2 | 安徽 |        1
  3 | 六安 |        2
  4 | 顺河 |        3

```

## JOIN
Postgresql join有5种方式：

- CROSS JOIN ：交叉连接
- INNER JOIN：内连接
- LEFT OUTER JOIN：左外连接
- RIGHT OUTER JOIN：右外连接
- FULL OUTER JOIN：全外连接

图解:  
![PostgreSQL_JOIN](/postgresql/postgresql_join.jpg)


## 批量插入

**方式一**：
```
INSERT INTO table_name SELECT ... FROM source_table;
```
例子
```text
test=# insert into t2(id) select generate_series(1,5);
INSERT 0 5
```

**方式二**：
```text
INSERT INTO t2(id) values(1),(2),(3);
```

**方式三**：
```text
COPY t2 FROM '/home/pg11/t2.txt';
```

## RETURNING 
RETURNING可以返回删除，修改，创建的数据
```text
INSERT INTO t2(id) values(100) RETURNING *;
UPDATE t2 SET id=10 WHERE id=1 RETURNING *;
DELETE FROM t2 WHERE id=2 RETURNING * ;
```

## INSERT
```text
[ WITH [ RECURSIVE ] with_query [, ...] ]
INSERT INTO table_name [ AS alias ] [ ( column_name [, ...] ) ]
    [ OVERRIDING { SYSTEM | USER} VALUE ]
    { DEFAULT VALUES | VALUES ( { expression | DEFAULT } [, ...] ) [, ...] | query }
    [ ON CONFLICT [ conflict_target ] conflict_action ]
    [ RETURNING * | output_expression [ [ AS ] output_name ] [, ...] ]
其中 conflict_target 可以是以下之一：
    ( { index_column_name | ( index_expression ) } [ COLLATE collation ] [ opclass ] [, ...] ) [ WHERE index_predicate ]
    ON CONSTRAINT constraint_name
并且 conflict_action 是以下之一：
    DO NOTHING
    DO UPDATE SET { column_name = { expression | DEFAULT } |
                    ( column_name [, ...] ) = [ ROW ] ( { expression | DEFAULT } [, ...] ) |
                    ( column_name [, ...] ) = ( sub-SELECT )
                  } [, ...]
              [ WHERE condition ]
```
案例：
with 里面更新之后，插入新值
```text
WITH upd AS (
  UPDATE employees SET sales_count = sales_count + 1 WHERE id =
    (SELECT sales_person FROM accounts WHERE name = 'Acme Corporation')
    RETURNING *
)
INSERT INTO employees_log SELECT *, current_timestamp FROM upd;
```
excluded用来引用原来要插入的值  
```text
test=# create table t3(id int primary key, name varchar(100));
CREATE TABLE
test=# insert into t3  values(1, 'aa'),(2, 'bb');
INSERT 0 2
test=# insert into t3  values(1, 'cc'),(2, 'dd') on conflict(id) do update set name=EXCLUDED.name;
INSERT 0 2
test=# select * from t3;
 id | name
----+------
  1 | cc
  2 | dd
```

## UPDATE
语法格式：
```text
[ WITH [ RECURSIVE ] with_query [, ...] ]
UPDATE [ ONLY ] table_name [ * ] [ [ AS ] alias ]
    SET { column_name = { expression | DEFAULT } |
          ( column_name [, ...] ) = [ ROW ] ( { expression | DEFAULT } [, ...] ) |
          ( column_name [, ...] ) = ( sub-SELECT )
        } [, ...]
    [ FROM from_list ]
    [ WHERE condition | WHERE CURRENT OF cursor_name ]
    [ RETURNING * | output_expression [ [ AS ] output_name ] [, ...] ]
```

使用子句的返回值作为更新方式：
```text
UPDATE accounts SET (contact_first_name, contact_last_name) =
    (SELECT first_name, last_name FROM salesmen
     WHERE salesmen.id = accounts.sales_id);
```
使用连接的方式更新值:
```text
UPDATE accounts SET contact_first_name = first_name,
                    contact_last_name = last_name
  FROM salesmen WHERE salesmen.id = accounts.sales_id;
```

更新一个统计语句
```text
UPDATE summary s SET (sum_x, sum_y, avg_x, avg_y) =
    (SELECT sum(x), sum(y), avg(x), avg(y) FROM data d
     WHERE d.group_id = s.group_id);
```