# Seata Server 部署

[toc]

## 1. 拉取 docker 镜像

```shell
docker pull seataio/seata-server:1.6.0
```



## 2. 使用 docker-compose 创建 Seata 的 docker 容器

### 2.1. 编写 docker-compose.yml 配置文件

说明: [参考官方文档](https://seata.io/zh-cn/docs/ops/deploy-by-docker-compose.html)  

``` yaml
version: "3.1"
services:
  seata-server:
    image: seataio/seata-server:1.6.0
    container_name: seata1.6.0
    ports:
      - "7091:7091"
      - "8091:8091"
    environment:
      - STORE_MODE=db
      # 以SEATA_IP作为host注册seata server
      - SEATA_IP=192.168.1.100
      - SEATA_PORT=8091
    volumes:
      - "/usr/share/zoneinfo/Asia/Shanghai:/etc/localtime"        #设置系统时区
      - "/usr/share/zoneinfo/Asia/Shanghai:/etc/timezone"  #设置时区
      # 假设我们通过docker cp命令把资源文件拷贝到相对路径`./seata-server/resources`中
      # 如有问题，请阅读上面的[注意事项]以及[使用自定义配置文件]
      - "./seata-server/resources:/seata-server/resources"
```

### 2.2. 构建 docker-compse 镜像

* (1). 从镜像中复制 **application.yml** 到 映射目录下

``` shell
docker cp seata-server:1.6.0:/seata-server/resources ./seata-server
```

* (2). 编辑 **application.yml** 配置文件，添加 nacos 配置

``` shell
vim ./seata-server/resources/application.yml 
```
**application.yml**

``` yaml
server:
  port: 7091

spring:
  application:
    name: seata-server

logging:
  config: classpath:logback-spring.xml
  file:
    path: ${user.home}/logs/seata
  extend:
    logstash-appender:
      destination: 127.0.0.1:4560
    kafka-appender:
      bootstrap-servers: 127.0.0.1:9092
      topic: logback_to_logstash

console:
  user:
    username: seata
    password: seata

seata:
  config:
    # support: nacos, consul, apollo, zk, etcd3
    type: nacos
    nacos:
      server-addr: 192.168.1.100:8848
      namespace: public
      group: my-cloud
      username: nacos
      password: nacos
      data-id: seataServer.yml
  registry:
    # support: nacos, eureka, redis, zk, consul, etcd3, sofa
    type: nacos
    nacos:
      application: seata-server
      server-addr: 192.168.1.100:8848
      group: my-cloud
      namespace: public
      # tc集群名称
      cluster: default
      username: nacos
      password: nacos
 # store:
    # support: file 、 db 、 redis
 #   mode: db
#  server:
#    service-port: 8091 #If not configured, the default is '${server.port} + 1000'
  security:
    secretKey: SeataSecretKey0c382ef121d778043159209298fd40bf3850a017
    tokenValidityInMilliseconds: 1800000
    ignore:
      urls: /,/**/*.css,/**/*.js,/**/*.html,/**/*.map,/**/*.svg,/**/*.png,/**/*.ico,/console-fe/public/**,/api/v1/auth/login
```

* (3). 在 Nacos 创建 Seata 的数据源配置文件 **seataServer.yml**

注意：配置文件名字必须与 **application.yml** 中的 `seata.config.nacos.data-id` 的值一致。

**seataServer.yml**

``` yaml
store:
  mode: db
  #-----db-----
  db: 
    datasource: druid
    dbType: mysql
    # 需要根据mysql的版本调整driverClassName
    # mysql8及以上版本对应的driver：com.mysql.cj.jdbc.Driver
    # mysql8以下版本的driver：com.mysql.jdbc.Driver
    driverClassName: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://192.168.1.100:3306/db_cloudac_seata?useUnicode=true&characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true&useSSL=false
    user: root
    password: ztom1023
    # 数据库初始连接数
    minConn: 1
    # 数据库最大连接数
    maxConn: 20
    # 获取连接时最大等待时间 默认5000，单位毫秒
    maxWait: 5000
    # 全局事务表名 默认global_table
    globalTable: global_table
    # 分支事务表名 默认branch_table
    branchTable: branch_table
    # 全局锁表名 默认lock_table
    lockTable: lock_table
    # 查询全局事务一次的最大条数 默认100
    queryLimit: 100
  
server:
  undo:   
    # undo保留天数 默认7天,log_status=1（附录3）和未正常清理的undo
    logSaveDays: 7
    # undo清理线程间隔时间 默认86400000，单位毫秒
    logDeletePeriod: 86400000
  # 二阶段提交重试超时时长 单位ms,s,m,h,d,对应毫秒,秒,分,小时,天,默认毫秒。默认值-1表示无限重试
  # 公式: timeout>=now-globalTransactionBeginTime,true表示超时则不再重试
  # 注: 达到超时时间后将不会做任何重试,有数据不一致风险,除非业务自行可校准数据,否者慎用
  maxCommitRetryTimeout: -1
  # 二阶段回滚重试超时时长
  maxRollbackRetryTimeout: -1
  # 二阶段提交未完成状态全局事务重试提交线程间隔时间 默认1000，单位毫秒
  recovery:
    committingRetryPeriod: 1000
    # 二阶段异步提交状态重试提交线程间隔时间 默认1000，单位毫秒
    asynCommittingRetryPeriod: 1000
    # 二阶段回滚状态重试回滚线程间隔时间  默认1000，单位毫秒
    rollbackingRetryPeriod: 1000
    # 超时状态检测重试线程间隔时间 默认1000，单位毫秒，检测出超时将全局事务置入回滚会话管理器
    timeoutRetryPeriod: 1000
```

* (4). 创建 docker 容器

``` shell 
# 创建容器
docker-compose up -d

# 设置容器自启动
docker update --restart=always seata1.6.0
```

