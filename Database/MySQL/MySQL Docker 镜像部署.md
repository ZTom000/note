# MySQL Docker 镜像部署

## 1. 获取 MySQL 镜像

```shell
docker pull mysql:8.0
```

## 2. 创建映射目录

目录路径：

data 路径：

`/mount/mysql/data`

配置文件路径：

`/mount/mysql/conf`

`/mount/mysql/conf/conf.d`

log 路径：

`/mount/mysql/log`

```shell
mkdir ./mysql/data
mkdir ./mysql/conf
mkdir ./mysql/conf/conf.d
mkdir ./mysql/log
```

## 3. 创建 MySQL 容器

```shell
# 创建容器
docker run -p 3310:3306 --name mysql-3310 --restart=always -v D:\mount\mysql\log:/var/log/mysql -v D:\mount\mysql\data:/var/lib/mysql -v D:\mount\mysql\conf:/etc/mysql -e MYSQL_ROOT_PASSWORD=root --privileged=true -d mysql:8.0
# 查看日志
```
