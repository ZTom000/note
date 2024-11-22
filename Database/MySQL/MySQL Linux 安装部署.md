# MySQL Linux 安装部署

[TOC]

## 1. 获取 MySQL 安装包并解压

```shell
# 下载安装包到指定目录
sudo wget https://downloads.mysql.com/archives/get/p/23/file/mysql-8.0.36-linux-glibc2.28-x86_64.tar.xz
# 解压安装包
sudo tar -xf ./mysql-8.0.36-linux-glibc2.28-x86_64.tar.xz
```

## 2. 配置 my.cnf 配置文档

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

## 3. 给对应 data 文件夹赋权，初始化 mysql 数据库

```shell
# 创建 data tmp logs 文件夹
sudo mkdir /usr/mysql/data 
sudo mkdir /usr/mysql/tmp 
sudo mkdir /usr/mysql/logs
# 给文件夹赋权
sudo chmod -R 777 /usr/mysql/

# 初始化 mysql 数据库
./bin/mysqld --defaults-file=./my.cnf --user=root --initialize
```

## 4. 启动数据库

```shell
# 启动 mysql
/usr/server/mysql-8.0.36/bin/mysqld_safe --defaults-file=./my.cnf 
# 进入数据库
mysql -h127.0.0.1 -uroot -P3206 -p
```

```sql
-- 修改密码
ALTER user 'root'@'localhost' IDENTIFIED BY 'Aa123456'
-- 设置远程访问权限
update user set host = "%" where user = "root";
-- 刷新权限
flush privileges;
```

## 5. 添加系统自启动

```shell
# 打开启动脚本
sudo vim /etc/rc.local 
# 在脚本中添加指令
/usr/server/mysql-8.0.36/bin/mysqld_safe --defaults-file=./my.cnf &
# 添加权限
sudo chmod +x /etc/rc.local
```

```sql
-- 创建远程访问对象
create user 'rep'@'%'identified by 'Aa123456';
-- 设置远程访问权限
```
