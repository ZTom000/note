# Spring IoC 容器

[toc]

## 1. IoC 概念

​		IoC：控制反转（ Inversion of Control ），即将 Bean 实例的创建与管理行为交给框架去处理，开发者只需要关注 Bean 的使用即可。

## 2. IoC 容器

### 2.1. BeanFactory  接口

#### 2.1.1. 概述

它是最简单的容器，给依赖注入（ DI ）提供了基本的支持，它用 org.springframework.beans.factory.BeanFactory 接口来定义。BeanFactory 或者相关的接口，如 BeanFactoryAware，InitializingBean，DisposableBean，在 Spring 中仍然存在具有大量的与 Spring 整合的第三方框架的反向兼容性的目的。

#### 2.1.2 XmlBeanFactory

这个容器从一个 XML 文件中读取配置元数据，由这些元数据来生成一个被配置化的系统或者应用。

#### 2.1.3. 注意事项

在资源宝贵的移动设备或者基于 applet 的应用当中， BeanFactory 会被优先选择。否则，一般使用的是 ApplicationContext，除非你有更好的理由选择 BeanFactory。

### 2.2. ApplicationContext  接口

#### 2.2.1 概述

该容器添加了更多的企业特定的功能，例如从一个属性文件中解析文本信息的能力，发布应用程序事件给感兴趣的事件监听器的能力。该容器是由  org.springframework.context.ApplicationContext 接口定义。 Application Context 是 BeanFactory 的子接口，也被称为 Spring 上下文。 Application Context 是 spring 中较高级的容器。和 BeanFactory 类似，它可以加载配置文件中定义的 bean，将所有的 bean 集中在一起，当有请求的时候分配 bean。

#### 2.2.2. 常用接口

1. **FileSystemXmlApplicationContext 接口**：该容器从 XML 文件中加载已被定义的 bean，需要提供给构造器 XML 文件的完整路径。
2. **ClassPathXmlApplicationContext 接口**：该容器从 XML 文件中加载已被定义的 bean。只需正确配置 CLASSPATH 环境变量即可，容器会从 CLASSPATH 中搜索 bean 配置文件。
3. **WebXmlApplicationContext 接口**： 该容器会在一个 web 应用程序的范围内加载在 XML 文件中已被定义的 bean。

