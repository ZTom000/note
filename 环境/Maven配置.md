# Maven 配置

[toc]

## 1. 下载Maven

[Maven 下载地址](http://maven.apache.org/download.cgi)

## 2. 解压安装包



![](D:\flies\环境\Images\maven目录.jpg)

## 3. 配置环境变量

```
Key   M2_HOME 
Value D:\apache-maven-3.8.2
```

![](D:\flies\环境\Images\M2_HOME.png)

```
Key   path
Value %M2_HOME%\bin
```

![PATH](D:\flies\环境\Images\PATH.png)

## 4.  配置 setting.xml

### 4.1. 配置阿里云镜像

打开 ./config 文件夹下的 setting.xml 

```xml
<mirror>
    <id>aliyunmaven</id>
    <mirrorOf>*</mirrorOf>
    <name>阿里云公共仓库</name>
    <url>https://maven.aliyun.com/repository/public</url>
</mirror>
```

配置代理仓库

```xml
<repository>
    <id>spring</id>
    <url>https://maven.aliyun.com/repository/spring</url>
    <releases>
        <enabled>true</enabled>
    </releases>
    <snapshots>
        <enabled>true</enabled>
    </snapshots>
</repository>
```

## 5. mvnrepository 仓库

网址：[mvnrepository.com/](https://link.juejin.cn?target=http%3A%2F%2Fmvnrepository.com%2F)

 

