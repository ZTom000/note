# SQLServer 食用指南

[TOC]

## 1. 环境部署

### 1.1. 软件包安装

### 1.2. 配置开启远程连接

- 打开 SSMS
  
  ![1678435439658](image/SQLServer食用指南/1678435439658.png)
  
  ![1678435458480](image/SQLServer食用指南/1678435458480.png)
  
  ![1678435471440](image/SQLServer食用指南/1678435471440.png)

- 打开 SSCM
  
  ![1678435486649](image/SQLServer食用指南/1678435486649.png)
  
  ![1678435496781](image/SQLServer食用指南/1678435496781.png)

- 打开防火墙设置对应端口规则

## 2. 常用SQL

### 2.1. 表控制

#### 2.1.1. 创建表设置自增 id

```sql
CREATE TABLE table_name (
    id BIGINT PRIMARY KEY IDENTITY(1, 1) NOT NULL
)
```

#### 2.1.2. 修改表

```sql
-- 删除列
ALTER TABLE table_name 
DROP COLUMN id;
-- 设置列自增 id
ALTER TABLE table_name  
ADD Id BIGINT 
IDENTITY(1, 1) NOT NULL;
```

#### 2.1.3. 清除表单数据

```sql
-- 重置表数据
TRUNCATE TABLE table_name;
```

#### 2.1.4. 删除表

```sql
DROP TABLE table_name;
```

#### 2.1.5 声明表变量

```sql
CREATE TYPE table_name AS TABLE (
    var1 NVARCHAR(200) NULL, 
    var2 NVARCHAR(200) NULL
)
```

### 2.2. SQL 编程

#### 2.2.1. 存储过程

1. 创建存储过程

```sql
CREATE PROCEDURE [dbo].[procedure_name]
@var1 NVARCHAR(50) INPUT,
@var2 INT OUTPU

AS
BEGIN
  DECLARE @var3 INT 
  DECLARE @table_var TABLE (
    id INT NULL,
    col INT NULL
)

  SET @var3 = 0
  -- DOSOMETHING 

  IF @var3 IS NULL 
    BEGIN
      RETURN 0
    END
  ELSE 
    BEGIN 
      RETURN @Sequence
   END
END
```

#### 2.2.2. 方法

```sql
CREATE FUNCTION [dbo].[function_name]
( 
@var1 INT,
@var2 INT
)
RETURNS NVARCHAR(300) -- 返回值
AS
BEGIN
    DECLARE @Result NVARCHAR(300)

    -- DOSOMETHING

    RETURN @Result
END
```

### 2.3. 变量使用

- 子查询返回的值多于一个。当子查询跟随在=、!=、<、<=、>、>= 之后，或子查询用作表达式时，这种情况是不被允许的。

```sql
CREATE TABLE table1( 
userid INT , 
addr VARCHAR(128)  
) 
GO 
INSERT INTO table1(userid, addr) VALUES(1, 'addr1') 
INSERT INTO table1(userid, addr) VALUES(2, 'addr2') 
INSERT INTO table1(userid, addr) VALUES(3, 'addr3') 
GO

DECLARE @addr varchar(128)
-- 报错
SET @addr = (SELECT addr FROM table1)
```

- 表达式返回多个值时，使用 SELECT 赋值 ，返回结果集最后一个 addr 列的值
  
  ```sql
  DECLARE @addr VARCHAR(128)
  SELECT @addr = addr FROM table1 
  PRINT @addr -- 结果集最后一个值
  ```

- 表达式未返回值时，使用 SET 赋值 ，返回结果为 NULL
  
  ```sql
  SET @addr = '初始值'
  SET @addr = (SELECT addr FROM table1 WHERE userid = 4 ) -- 返回 NULL
  ```

- 表达式未返回值时，使用 SELECT 赋值，返回值为原值
  
  ```sql
  SET @addr = '初始值'
  SELECT @addr = addr FROM table1 WHERE userid = 4 -- 返回原值
  ```

- 需要注意的是，SELECT 也可以将标量子查询的值赋给变量，如果标量子查询不返回值，则变量被置为 NULL 值。   此时与使用 SET 赋值是完全相同的。

### 2.3. 常用函数

#### 2.3.1. PIVOT() 函数

说明：可以使用 `PIVOT` 和 `UNPIVOT` 关系运算符将表值表达式更改为另一个表。 `PIVOT` 通过将表达式中的一个列的唯一值转换为输出中的多列，来轮替表值表达式。 `PIVOT` 在需要对最终输出所需的所有剩余列值执行聚合时运行聚合。 与 PIVOT 执行的操作相反，`UNPIVOT` 将表值表达式的列轮换为列值。

```sql
-- 使用方法
SELECT <non-pivoted column>,  
    [first pivoted column] AS <column name>,  
    [second pivoted column] AS <column name>,  
    ...  
    [last pivoted column] AS <column name>  
FROM  
    (<SELECT query that produces the data>)   
    AS <alias for the source query>  
PIVOT  
(  
    <aggregation function>(<column being aggregated>)  
FOR   
[<column that contains the values that will become column headers>]   
    IN ( [first pivoted column], [second pivoted column],  
    ... [last pivoted column])  
) AS <alias for the pivot table>  
<optional ORDER BY clause>;  
```
