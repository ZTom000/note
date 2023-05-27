# Spring Boot 服务部署

[TOC]

## 1. 手动部署

### 1.1. 下载 WinSW.EXE

地址：`https://github.com/winsw/winsw/releases`

### 1.2. 部署后端项目

步骤: 

1. 将打包好的 Spring-Boot 应用拷到部署目录下。

2. 将 WinSW.EXE 应用拷到部署目录下，并重命名为服务名，如`service-name`。

3. 新建 `service-name.xml` 配置文件，如下方示例代码所示。

4. 在 cmd 执行指令 `service-name install` 安装应用。

5. 在 Windows 服务里面将该服务设置为自动启动。

6. 若需要远程访问则需在防火墙里配置该端口的出入站规则。

```xml
<service>
	<id>service-name</id>
	<name>service-name</name>
	<description>service-name description</description>
	<executable>java</executable>
	<arguments>-jar service-name.jar</arguments>
	<logmode>rotate</logmode>
</service>
```

### 
