# Seata 使用笔记

[TOC]

## 1. 服务端使用方法

[详见笔记](D:\flies\Seata\Seata Server 部署笔记.md)

## 2. Client 端使用方法

### 2.1. 导入 Maven 依赖

```xml
        <!-- seata  -->
        <dependency>
            <groupId>io.seata</groupId>
            <artifactId>seata-spring-boot-starter</artifactId>
            <version>${seata-spring-boot-starter.version}</version>
        </dependency>
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-seata</artifactId>
            <version>${spring-cloud-starter-alibaba-seata.version}</version>
            <exclusions>
                <exclusion>
                    <groupId>io.seata</groupId>
                    <artifactId>seata-spring-boot-starter</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
```

### 2.2. 编写服务 application.yml / bootstrap.yml

```yml
# 配置 seata 基本信息
seata:
  application-id: ${spring.application.name}
  # 配置事务分组（与 seata-server 服务端配置的值须一致）
  tx-service-group: my-cloud-group
  service:
    vgroup-mapping:
      my-cloud-group: default
  enabled: true
  # 配置注册中心信息，这里我选用 nacos
  registry:
    type: nacos
    nacos:
      cluster: default
      server-addr: 192.168.1.100
      username: nacos
      password: nacos
      group: my-cloud
```

### 2.3.

## 3. TCC 模式

### 3.1. TCC 原理（try-confirm-cancel）

首先回顾一下TCC模式

#### [#](https://cloud.benym.cn/#tcc模式原理) TCC模式原理

TCC模式与AT模式非常相似，每阶段都是独立事务，不同的是TCC通过人工编码来实现数据恢复。需要实现三个方法：

- Try：资源的检测和预留；
- Confirm：完成资源操作业务；要求Try成功Confirm一定要能成功。
- Cancel：预留资源释放，可以理解为Try的反向操作。 举例，一个扣减用户余额的业务。假设账户A原来余额是100，需要余额扣减30元。

### 3.X 实际问题

#### 3.x.1. 防悬挂

#### 3.X.2 空回滚

### 
