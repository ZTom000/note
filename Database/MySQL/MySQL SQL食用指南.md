# MySQL SQL 食用指南

[toc]

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
