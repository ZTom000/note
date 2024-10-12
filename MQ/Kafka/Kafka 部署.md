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
# 设置 log 目录
log.dirs=./logs/kafka-logs
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
 cd ./kafka_2.13-3.8.0 sudo ./bin/kafka-server-start.sh ./config/server.properties
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










