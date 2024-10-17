# Redis 安装与配置

[TOC]

## 1. Redis 安装 ( Linux 环境)

## 2. 配置文件

### 2.1. 设置允许远程访问

```shell
vim ./redis.conf
```

```
# 注释掉 bind 127.0.0.1
# bind 127.0.0.1
# 修改 protected-mode
protected-mode no
# 设置后台启动 
daemonize yes
# 设置密码
requirepass password
```

设置防火墙放行 6379 端口

## 3. 配置 redis 自启动服务

### 3.1. 修改配置文件

```shell
# 打开 redis 配置文件
sudo vim ./redis.conf 
```

redis.conf 

```properties
# 修改 supervised 配置 
supervised systemd
# 注释掉 bind 127.0.0.1
# bind 127.0.0.1
# 修改 protected-mode
protected-mode no
# 设置后台启动 
daemonize yes
# 设置密码
requirepass password
```

### 3.2. 新建启动服务

```shell
# 进入 systemd 目录 
cd /lib/systemd/system
# 编辑 redis.service 文件
sudo vim redis.service
# 刷新 systemctl 配置
systemctl daemon-reload
# 配置 redis
# 启动 redis 服务
systemctl start redis
# 查看 redis 服务 
systemctl status redis
# 将 redis 服务设置为开机启动 
systemctl enable redis
# 关闭 redis 服务
systemctl stop redis
```

redis.service 设置: 

```properties
[Unit]
Description=Redis Server
After=network.target remote-fs.target

[Service]
Type=forking
User=root
Group=root
#Environment="PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
ExecStart=/usr/bin/redis-server /etc/redis/redis.conf
ExecStop=redis-cli -h 127.0.0.1 -p 6379 -a root  shutdown
Restart=on-failure

[Install]
WantedBy=multi-user.target


```
