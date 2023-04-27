# CMD 指令攻略

## 1. 关闭 SpringBoot 容器进程

```shell
# 查看指定端口使用情况
netstat -ano | findstr 端口号
# 查看指定进程
tasklist | findstr <PID>
# 关闭指定指令
taskkill /PID pid号 /T /F
```
