# Nacos 环境部署

[TOC]

## 1. nacos 自启动配置

```shell
# 重启服务
systemctl daemon-reload
# 设置 nacos 开机自启动
systemctl enable nacos.service
# 查看 nacos 服务状态
systemctl status nacos
# 启动 nacos 服务
systemctl start nacos
# 关闭 nacos 服务
systemctl stop nacos
# 重启 nacos 服务
systemctl restart nacos
```

nacos 自启动脚本

```shell
[Unit]
Description=nacos
After=network.target

[Service] 
# java安装位置
Environment="JAVA_HOME=/usr/java/jdk1.8.0_121" 
Type=forking
ExecStart=/usr/nacos/nacos/bin/startup.sh -m cluster
ExecReload=/usr/nacos/nacos/bin/shutdown.sh
ExecStop=/usr/nacos/nacos/bin/shutdown.sh
PrivateTmp=true

[Install]
WantedBy=multi-user.target
```
