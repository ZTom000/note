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
