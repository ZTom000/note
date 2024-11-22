# MySQL 创建双写集群

[TOC]

## 1. 创建 MySQL 实例

[MySQL Linux 安装部署](./MySQL Linux 安装部署.md)

**说明: 在配置主从同步之前请先将数据库的结构与数据从主库复制到从库，具体操作步骤此文档省略**

## 2. 创建 MySQL 用户

```sql
-- 创建用于同步的用户
create user 'rep'@'%' identified by 'Aa123456'
-- 设置用于同步的权限
grant replication slave,replication client on *.* to 'rep'@'%';
-- 更新权限
flush privileges;
```

## 3. 配置主节点 my.cnf

```shell
# 查看 MySQL my.cnf 目录
ps -ef|grep mysql
# 修改对应目录的 my.cnf
sudo vim ./my.cnf
```

```shell
[client]
#password       = your_password
port            = 3306
socket          = /tmp/mysql.sock

[mysqld]
port            = 3306
socket          = /tmp/mysql.sock
datadir = /www/server/data
default_storage_engine = InnoDB
performance_schema_max_table_instances = 400
table_definition_cache = 400
skip-external-locking
key_buffer_size = 512M
max_allowed_packet = 100G
table_open_cache = 2048
sort_buffer_size = 8M
net_buffer_length = 4K
read_buffer_size = 8M
read_rnd_buffer_size = 256K
myisam_sort_buffer_size = 128M
thread_cache_size = 256
tmp_table_size = 256M
default_authentication_plugin = mysql_native_password
lower_case_table_names = 1
sql-mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES

explicit_defaults_for_timestamp = true
#skip-name-resolve
max_connections = 500
max_connect_errors = 100
open_files_limit = 65535
# 重要配置 bin-log
log-bin=mysql-bin
binlog_format=mixed
server-id = 1
binlog_expire_logs_seconds = 600000
slow_query_log=1
slow-query-log-file=/www/server/data/mysql-slow.log
long_query_time=3
#log_queries_not_using_indexes=on
early-plugin-load = ""
# 重要 配置双主节点 必须配置
log-slave-updates = true
# 重要 配置双主节点 必须配置主键 offset
auto_increment_offset = 1
auto_increment_increment = 2

# 配置同步配置项
gtid-mode=on
enforce-gtid-consistency=true
master-info-repository=TABLE
relay-log-info-repository=TABLE
sync-master-info=1
slave-parallel-workers=0
sync_binlog=0
binlog-checksum=CRC32
master-verify-checksum=1
slave-sql-verify-checksum=1
binlog-rows-query-log_events=1
# 配置同步数据库 重复配置可复制相同的
binlog-do-db = hr_wz

init-connect = 'SET NAMES utf8'
character-set-server = utf8

innodb_data_home_dir = /www/server/data
innodb_data_file_path = ibdata1:10M:autoextend
innodb_log_group_home_dir = /www/server/data
innodb_buffer_pool_size = 2048M
innodb_log_file_size = 1024M
innodb_log_buffer_size = 256M
innodb_flush_log_at_trx_commit = 1
innodb_lock_wait_timeout = 50
innodb_max_dirty_pages_pct = 90
innodb_read_io_threads = 4
innodb_write_io_threads = 4

[mysqldump]
quick
max_allowed_packet = 500M

[mysql]
no-auto-rehash

[myisamchk]
key_buffer_size = 512M
sort_buffer_size = 8M
read_buffer = 2M
write_buffer = 2M

[mysqlhotcopy]
interactive-timeout
```

## 4. 配置副节点 my.cnf

```shell
[client]
#password       = your_password
port            = 3206
mysqlx-port = 32060
mysqlx-bind-address = 127.0.0.1
socket          = /usr/mysql/tmp/mysql.sock

[mysqld]
binlog_cache_size = 256K
thread_stack = 512K
join_buffer_size = 8192K
max_heap_table_size = 2048M
port            = 3206
socket          = /usr/mysql/tmp/mysql.sock
datadir = /usr/mysql/data
default_storage_engine = InnoDB
performance_schema_max_table_instances = 400
table_definition_cache = 400
skip-external-locking
key_buffer_size = 1024M
max_allowed_packet = 100G
table_open_cache = 2048
sort_buffer_size = 4096K
net_buffer_length = 4K
read_buffer_size = 4096K
read_rnd_buffer_size = 2048K
myisam_sort_buffer_size = 64M
thread_cache_size = 256
tmp_table_size = 2048M
default_authentication_plugin = mysql_native_password
lower_case_table_names = 1
sql-mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES

explicit_defaults_for_timestamp = true
#skip-name-resolve
max_connections = 500
max_connect_errors = 100
open_files_limit = 65535

log-bin=mysql-bin
binlog_format=mixed
server-id = 11
binlog_expire_logs_seconds = 600000
slow_query_log=1
slow-query-log-file=/usr/mysql/logs/mysql-slow.log
long_query_time=3
#log_queries_not_using_indexes=on
early-plugin-load = ""
log-slave-updates = true
auto_increment_offset = 2
auto_increment_increment = 2

gtid-mode=on
enforce-gtid-consistency=true
master-info-repository=TABLE
relay-log-info-repository=TABLE
sync-master-info=1
slave-parallel-workers=0
sync_binlog=0
binlog-checksum=CRC32
master-verify-checksum=1
slave-sql-verify-checksum=1
binlog-rows-query-log_events=1

binlog-do-db = hr_wz

init-connect = 'SET NAMES utf8'
character-set-server = utf8

innodb_data_home_dir = /usr/mysql/data
innodb_data_file_path = ibdata1:10M:autoextend
innodb_log_group_home_dir = /usr/mysql/logs
innodb_buffer_pool_size = 4096M
innodb_log_file_size = 512M
innodb_log_buffer_size = 128M
innodb_flush_log_at_trx_commit = 1
innodb_lock_wait_timeout = 50
innodb_max_dirty_pages_pct = 90
innodb_read_io_threads = 8
innodb_write_io_threads = 8

[mysqldump]
quick
max_allowed_packet = 500M

[mysql]
no-auto-rehash

[myisamchk]
key_buffer_size = 256M
sort_buffer_size = 4M
read_buffer = 2M
write_buffer = 2M

[mysqlhotcopy]
interactive-timeout
```

## 5. 在 MySQL 中设置主从库

```shell
# 登录 mysql
mysql -h127.0.0.1 -P3306 -uroot -p 
# 输入密码 
```

```sql
-- 查看主节点状态
show master status;
-- 记录主节点状态
-- +------------------+----------+--------------+------------------+-----------------------------------------------------------------------------------------+
-- | File             | Position | Binlog_Do_DB | Binlog_Ignore_DB | Executed_Gtid_Set                                                                       |
-- +------------------+----------+--------------+------------------+-----------------------------------------------------------------------------------------+
-- | mysql-bin.000025 |    25554 | hr_wz        |                  | 2475bd77-4ff8-11ef-8c35-000c2918f994:1-15,
-- b3473688-a7ed-11ef-a0c0-f4ce46ac40b0:1:16-37 |
-- +------------------+----------+--------------+------------------+-----------------------------------------------------------------------------------------+
-- 设置从节点的主节点状态
change master to master_host='master-ip',master_port=3306,master_user='rep',master_password='Aa123456',master_log_file='mysql-bin.000025',master_log_pos=157;
-- 开启同步
start slave;
-- 查看同步状态
show slave status\G;
-- 查看显示的状态是否如如下
-- Slave_IO_Running: Yes
-- Slave_SQL_Running: Yes
-- 若为上述状态则可以进行测试数据是否能同步写入

-- 当出现bin-log 同步异常时,停止主从同步
STOP REPLICA IO_THREAD FOR CHANNEL '';
-- 重新设置主节点状态与 bin-log 文件与位移
change master to master_host='master-ip',master_port=3306,master_user='rep',master_password='Aa123456',master_log_file='mysql-bin.000025',master_log_pos=******;
-- 重新开始开启同步
start slave;
-- 查看同步状态
show slave status\G;
```

## 6. 测试两个数据库同步功能
