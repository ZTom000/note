# Kafka 部署文档

[TOC]

## 1. linux 环境

### 1.1. 下载软件

#### 1.1.1. 下载 zookeeper

```shell
# 下载 zookeeper
sudo wget https://dlcdn.apache.org/zookeeper/zookeeper-3.8.4/apache-zookeeper-3.8.4-bin.tar.gz
```

#### 1.1.2. 下载 kafka

```shell
# 下载 kafka
sudo wget https://downloads.apache.org/kafka/3.8.0/kafka_2.13-3.8.0.tgz
```

#### 1.2. 安装软件

#### 1.2.1. 解压软件包

```shell
# 解压 zookeeper 
sudo tar -zxvf ./apache-zookeeper-3.8.4-bin.tar.gz
# 解压 kafka
sudo tar -zxvf ./kafka_2.13-3.8.0.tgz
```

#### 1.2.2. 修改配置文件

zookeeper 配置文件：

```shell
# 进入 zookeeper conf 文件夹
cd ./apache-zookeeper-3.8.4-bin/conf
# 复制 zoo_sample.cfg 文件
sudo cp ./zoo_sample.cfg ./zoo.cfg
# 打开 zoo.cfg 文件
sudo vim ./zoo.cfg
```

修改配置文件：

```properties
# zoo.cfg
# 设置数据路径
dataDir=./data/zookeeper
# 设置日志路径
dataLogDir=./log/zookeeper
# 设置 admin 服务的 port. ( 默认使用 8080 端口，当 8080 端口被占用时请设置该项 )
admin.serverPort = 2190
```

kafka 配置文件:

```shell
# 进入 kafka 目录
sudo ./kafka_2.13-3.8.0/config
# 打开配置文件
sudo vim ./server.properties
```

修改配置文件：

```properties
# server.properties
# 配置 kafka 地址
advertised.listeners=PLAINTEXT://10.1.46.5:9092
# 设置 log 目录, 必须是绝对路径,否则使用自动启动服务会出现错误 cluster-id 不匹配的错误
log.dirs=/usr/server/kafka_2.13-3.8.0/logs/kafka-logs
```

### 1.3. 启动 kafka

```shell
# 启动 zookeeper 
cd ./apache-zookeeper-3.8.4-bin
sudo ./bin/zkServer.sh start
# 查看 zookeeper 状态
sudo ./bin/zkServer.sh status
# 关闭 zookeeper 
sudo ./bin/zkServer.sh stop
# 启动 kafka
cd ./kafka_2.13-3.8.0 
sudo ./bin/kafka-server-start.sh ./config/server.properties
/
```

### 1.4. 测试 kafka

```shell
# 创建测试 Topic
bin/kafka-topics.sh --create --topic quickstart-events --bootstrap-server localhost:9092
# 使用生产者发送消息
bin/kafka-console-producer.sh --topic quickstart-events --bootstrap-server localhost:9092
# > 输入要传输的消息内容
# 使用消费者消费消息 
bin/kafka-console-consumer.sh --topic quickstart-events --from-beginning --bootstrap-server localhost:9092
```

### 1.5. 配置 kafka 自启动服务

```shell
# 进入 systemd 目录 
cd /lib/systemd/system
# 编辑 zookeeper.service 文件
sudo vim zookeeper.service

# 编辑 kafka.service 文件
sudo vim kafka.service
# 刷新 systemctl 配置
systemctl daemon-reload
# 配置 zookeeper
# 启动 zookeeper 服务
systemctl start zookeeper
# 查看 zookeeper 服务 
systemctl status zookeeper
# 将 zookeeper 服务设置为开机启动 
systemctl enable zookeeper
# 关闭 zookeeper 服务
systemctl stop zookeeper
# 配置 kafka
# 启动 kafka 服务
systemctl start kafka
# 查看 kafka 服务 
systemctl status kafka 
# 将 kafka 服务设置为开机启动 
systemctl enable kafka
# 关闭 kafka 服务
systemctl stop kafka
```

kafka.service 文件: 

```properties
[Unit]
Description=Apache Kafka Server
After=network.target remote-fs.target zookeeper.service

[Service]
# 当 Type=forking 失效时考虑使用 Type=simple
Type=simple
User=root
Group=root
Environment="PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
ExecStart= /usr/server/kafka_2.13-3.8.0/bin/kafka-server-start.sh /usr/server/kafka_2.13-3.8.0/config/server.properties
ExecStop=/usr/server/kafka_2.13-3.8.0/bin/kafka-server-stop.sh
Restart=on-failure

[Install]
WantedBy=multi-user.target
```

zookeeper.service 文件:

```properties
[Unit]
Description=Apache Zookeeper Server
After=network.target remote-fs.target

[Service]
Type=forking
User=root
Group=root
Environment="PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
ExecStart=/usr/server/apache-zookeeper-3.8.4-bin/bin/zkServer.sh start
ExecStop=/usr/server/apache-zookeeper-3.8.4-bin/bin/zkServer.sh stop
Restart=on-failure

[Install]
WantedBy=multi-user.target
```
