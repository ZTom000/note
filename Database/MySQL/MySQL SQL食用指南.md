# MySQL SQL 食用指南

[TOC]

## 1. 常用 SQL

### 1.1. 表操作

#### 1.1.1. 查询表结构

```sql
show columns from table_name;
```

#### 1.1.2. 查询创建表 SQL 语句

```sql
show create table table_name;
```

#### 1.1.3. 获取表的元数据

```sql
use information_schema
select * from columns where table_name = 'table_name';
```

### 1.2. SQL 用法

#### 1.2.1. GROUP_CONCAT 通过 GROUP BY 合并所需列的所有元素

```sql
# a_id 为 GROUP BY 条件
SELECT a_id, 
    GROUP_CONCAT(DISTINCT colunm1 ORDER BY colunm1 DESC SEPARATOR ',')
FROM table_a
GROUP BY a_id 
```

说明：其中 `DISTINCT` 关键字为去重，`ORDER BY colunm1 DESC` 是根据 `colunm1` 倒序排序,  `SEPARATOR ','` 为根据`,`分隔每个值
