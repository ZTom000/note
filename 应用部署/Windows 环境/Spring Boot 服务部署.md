# Spring Boot 服务部署

[TOC]

## 1. 手动部署

### 1.1. 下载安装包

地址：`https://tomcat.apache.org/download-80.cgi`

### 1.2. 解压安装包

步骤: 

1. 使用解压软件解压 Tomcat 安装包

2. 将解压好的 Tomcat 拷到需要部署的目录下

### 1.3. 前端项目部署

 步骤: 

1. 将编译好的前端项目拷到 tomcat 目录下的 `webapp` 目录下面。

2. 修改 `webapp` 目录下的前端项目名(若需要默认路径可将前端项目放到 `ROOT` 目录下)。

3. 进入 `bin` 目录下执行 `startup.bat` 脚本。

4. 进入`bin` 目录下修改`server.xml` 配置文件。

5. 输入 URL 查看服务启动后网站是否能访问。

`server.xml` 示例：

```xml
<!-- 此处为关闭端口号，修改为自己服务所需端口号 -->
<Server port="8005" shutdown="SHUTDOWN">

<!-- 此处为访问端口号，修改为自己服务所需端口号 -->
<Connector port="8080" protocol="HTTP/1.1" connectionTimeout="20000" 
    redirectPort="8443" />


<!-- 此处为 AJP 端口号，修改为自己服务所需端口号 -->    
<Connector port="8009" protocol="AJP/1.3" redirectPort="8443" />
```

### 1.4. 设置添加系统服务

步骤：

1. 修改 `bin\service.bat` 文件中的 `SERVICE_NAME` 为所需服务名

2. 在 cmd 进入目录 `tomcat\bin\` 执行指令`service.bat install` 将 Tomcat 添加到系统服务中。

3. 在运行 ( win + r ) 中输入 `service.msc` 进入服务管理，在服务管理中找到 `Apache Tomcat ver your_service_name` 服务设置启动类型为自动

```batch
set SERVICE_NAME=your_service_name
```

```cmd
# 安装服务
service install 

# 安装指定服务名服务
service install server-name

# 卸载服务
service uninstall 

# 卸载指定服务名服务
service uninstall server-name

# 卸载服务
service remove 
```
