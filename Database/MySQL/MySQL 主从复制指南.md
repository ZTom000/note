# MySQL 主从复制指南

[TOC]

## 1. Master 服务器配置

- 修改主服务器 my.cnf/my.ini 文件

```shell
[mysqld]
log-bin = master-bin
log-bin-index = master-bin.index
server-id = 1
```

- 创建 `repl_user` 用户
  
  ```sql
  CREATE USER repl_user IDENTIFIED BY password;
  GRANT REPLCATION SLAVE ON *.* TO repl_user;
  ```

- 重启服务

## 2. Slave 服务器配置

- 修改从服务器 my.cnf/my.ini 文件

```shell
[mysqld]
relay-log = slave-relay-bin
relay-log-index = slave-relay-bin.index
server-id = 2
replicate-do-db = databasename
```

- 重启服务
