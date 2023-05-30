# Srping Boot 整合 Log4j2 日志框架

[toc]

## 1. 加入 Log4j2 依赖

pom.xml 示例：

``` xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-log4j2</artifactId>
        </dependency>
```

## 2. 移除 Spring Boot 默认的日志输出框架

pom.xml 示例：

```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
            <exclusions>
                <!--排除这个默认的日志组件，一定要有！！！-->
                <exclusion>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-starter-logging</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <exclusions>
                <!--排除这个默认的日志组件，一定要有！！！-->
                <exclusion>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-starter-logging</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
```

## 3. 在 application.yml 中添加 log 日志配置项

  示例：

```yml
logging:
  config: classpath:log4j2.yml
  level:
    com: debug

```

## 4. 在 resources 目录下添加日志配置文件 log4j2.yml

示例：

```yml
Configuration:
  status: warn

  Properties: # 定义全局变量
    Property: # 缺省配置（用于开发环境）。其他环境需要在VM参数中指定，如下：
      #测试：-Dlog.level.console=warn -Dlog.level.xjj=trace
      #生产：-Dlog.level.console=warn -Dlog.level.xjj=info
      - name: log.level.console
        value: debug
      - name: log.level.xjj
        value: info
      - name: log.path
        value: logs\${project.name}_log
      - name: project.name
        value: psm-device

  Appenders:
    Console:  #输出到控制台
      name: CONSOLE
      target: SYSTEM_OUT
      ThresholdFilter:
        level: ${sys:log.level.console} # “sys:”表示：如果VM参数中没指定这个变量值，则使用本文件中定义的缺省全局变量值
        onMatch: ACCEPT
        onMismatch: DENY
      PatternLayout:
        pattern: "%d{yyyy-MM-dd HH:mm:ss.SSS} %-5level %class{36} %L %M - %msg%xEx%n"
    RollingFile: # 输出到文件，超过100MB归档
      - name: ROLLING_FILE
        ignoreExceptions: false
        fileName: ${log.path}/${project.name}.log
        filePattern: "${log.path}/$${date:yyyy-MM}/${project.name}-%d{yyyy-MM-dd}-%i.log.gz"
        PatternLayout:
          pattern: "%d{yyyy-MM-dd HH:mm:ss.SSS} %-5level %class{36} %L %M - %msg%xEx%n"
        Policies:
          SizeBasedTriggeringPolicy:
            size: "100 MB"
        DefaultRolloverStrategy:
          max: 1000

  Loggers:
    Root:
      level: info
      AppenderRef:
        - ref: CONSOLE
        - ref: ROLLING_FILE
    Logger: # 为org.springframework包配置特殊的Log级别，方便调试
      - name: org.springframework
        additivity: false
        level: ${sys:log.level.xjj}
        AppenderRef:
          - ref: CONSOLE
          - ref: ROLLING_FILE
```
