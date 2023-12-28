# Maven 食用指南

[TOC]

## 1. Maven 打包 Spring Boot 项目

解决打包成 jar 包后出现无法找到主类的问题

```xml
<!-- pom.xml 片段 -->
    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <version>${spring-boot.version}</version>
                <configuration>
                    <mainClass>com.ztom.DemoApplication</mainClass>
                </configuration>
                <executions>
                    <execution>
                        <goals>
                            <goal>repackage</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
```

## 2. IDEA环境下 Java Web 项目集成 Maven

1. 编辑 pom.xml 文件

2. 将项目构建为 maven 项目

3. 设置配置 Tomcat Server 服务器配置
   
   - 在 Project Structure 中设置 Artifacts 设置 Output directory 中的路径指向 target文件夹
   
   - 在 Available Elements 选项中选中项目文件夹， 右键 Put into Output Root
     
     
