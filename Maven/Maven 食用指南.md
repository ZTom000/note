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

注意事项：

- 当 Libraries 没有 maven 依赖时需要重新加载 maven 项目。

- 当 module 出问题时首先要在 Setting 里面的 Maven 选项卡里的 Runner 选项勾选 Delegate IDE build/run actions to Maven 选项。然后在 Project Structure 的 Facets 选项卡下找到 fix 按钮，修复 module。 
