# Spring Bean

[toc]

 ## 1. Bean 

### 1.1. Bean 定义

 Bean 是 Spring 框架在运行时管理的对象。 Bean 是任何 Spring 应用程序的基本构建块。

### 1.2. 元数据配置

<table>
    <tr>
    	<th>属性</th>
    	<th>描述</th>
    </tr>
    <tr>
    	<td>class</td>
    	<td>这个属性是强制性的，并且指定用来创建 Bean 的 Bean 类。</td>
    </tr>
    <tr>
    	<td>name</td>
    	<td>这个属性指定唯一的 Bean 标识符。在基于 XML 的配置元数据中，你可以使用 ID 和/或 name 属性来指定 Bean 标识符。</td>
    </tr>
    <tr>
    	<td>scope</td>
    	<td>这个属性指定由特定的 Bean 定义创建的对象的作用域，它将会在 Bean 作用域的章节中进行讨论。</td>
    </tr>
    <tr>
    	<td>constructor-arg</td>
    	<td>/</td>
    </tr>
    <tr>
    	<td>properties</td>
    	<td>/</td>
    </tr>
    <tr>
    	<td>autowiring mode</td>
    	<td>/</td>
    </tr>
    <tr>
    	<td>lazy-initialization mode</td>
    	<td>延迟初始化的 bean 告诉 IoC 容器在它第一次被请求时，而不是在启动时去创建一个 bean 实例。</td>
    </tr>
    <tr>
    	<td>initialization</td>
    	<td> 在 Bean 的所有必需的属性被容器设置之后，调用回调方法。它将会在 Bean 的生命周期章节中进行讨论。</td>
    </tr>
    <tr>
    	<td>destruction</td>
    	<td>当包含该 Bean 的容器被销毁时，使用回调方法。它将会在 Bean 的生命周期章节中进行讨论。</td>
    </tr>
</table>


### 1.3. Bean 的作用域

#### 1.3.1. 定义

当在 Spring 中定义一个 Bean 时，必须声明该 Bean 的作用域的选项。例如，为了强制 Spring 在每次需要时都产生一个新的 Bean 实例，应该声明 Bean 的作用域的属性为 **prototype**。同理，如果你想让 Spring 在每次需要时都返回同一个bean实例，你应该声明 bean 的作用域的属性为 **singleton**。

<table>
    <tr>
        <th>作用域</th>
        <th>描述</th>
    </tr>
    <tr>
    	<td>singleton</td>
        <td>在Spring IoC容器仅存在一个 Bean 实例，Bean 以单例方式存在，默认值</td>
    </tr>
    <tr>
    	<td>prototype</td>
        <td>每次从容器中调用Bean时，都返回一个新的实例，即每次调用getBean()时，相当于执行newXxxBean()</td>
    </tr>    
    <tr>
    	<td>request</td>
        <td>每次HTTP请求都会创建一个新的Bean，该作用域仅适用于WebApplicationContext环境</td>
    </tr>    
    <tr>
    	<td>session</td>
        <td>同一个HTTP Session共享一个Bean，不同Session使用不同的Bean，仅适用于WebApplicationContext环境</td>
    </tr>    
    <tr>
    	<td>global-session</td>
        <td>一般用于Portlet应用环境，该作用域仅适用于WebApplicationContext环境</td>
    </tr>
</table>


**示例：**

``` xml
<!-- A bean definition with singleton scope -->
<bean id="..." class="..." scope="singleton">
    <!-- collaborators and configuration for this bean go here -->
</bean>
```

#### 1.3.2. singleton

说明：singleton 是默认的作用域，当定义 Bean 时，如果没有指定作用域配置项，则 Bean 的作用域被默认为 singleton 。当一个 Bean 的作用域为 singleton 时， Spring IoC 容器中只会存在一个共享的 bean 实例，并且所有对 Bean 的请求，只要 id 与该 bean 定义相匹配，则只会返回 Bean 的同一实例（非线程安全）。

#### 1.3.3. prototype

说明：当一个 Bean 的作用域为 prototype，表示一个 Bean 定义对应多个对象实例。prototype 作用域的 Bean  会导致在每次对该 Bean 请求（将其注入到另一个 Bean 中，或者以程序的方式调用容器的 getBean() 方法）时都会创建一个新的  Bean 实例。prototype 是原型类型，它在我们创建容器的时候并没有实例化，而是当我们获取 Bean 的时候才会去创建一个对象，而且我们每次获取到的对象都不是同一个对象。根据经验，对有状态的 Bean 应该使用 prototype 作用域，而对无状态的 Bean 则应该使用 singleton 作用域。

## 2. Bean 生命周期

### 2.1. 定义

Spring Bean 生命周期的4个阶段：

1. 实例化（ Instantiation ）
2. 属性赋值（ Populate ）
3. 初始化（ Initialization ）
4. 销毁（ Destruction ）

**过程：**实例化 -> 属性赋值 -> 初始化 -> 销毁

### 2.2. 创建过程

**如图：**

![Spring Bean 生命周期](D:\flies\Spring\images\spring-bean-生命周期01.jpg)

**步骤：**	

1. Spring 启动，查找并加载需要被 Spring 管理的 Bean，进行 Bean 的实例化。
2. Bean 实例化后对将 Bean 的引入和值注入到 Bean 的属性中。
3. 如果 Bean 实现了 `BeanNameAware` 接口的话， Spring 将 Bean 的 Id 传递给 `setBeanName()`  方法。
4. 如果 Bean 实现了 `BeanFactoryAware` 接口的话，Spring 将调用 `setBeanFactory()`  方法，将 BeanFactory 容器实例传入。
5. 如果 Bean 实现了 `ApplicationContextAware` 接口的话，Spring 将调用 Bean 的 `setApplicationContext()` 方法，将 bean 所在应用上下文引用传入进来。
6. 如果 Bean 实现了 `BeanPostProcessor` 接口，Spring 就将调用他们的 `postProcessBeforeInitialization()` 方法。
7. 如果 Bean 实现了 `InitializingBean` 接口，Spring 将调用他们的 `afterPropertiesSet()` 方法。类似的，如果 Bean 使用 `init-method` 声明了初始化方法，该方法也会被调用。
8. 如果 Bean 实现了 `BeanPostProcessor` 接口，Spring 就将调用他们的 `postProcessAfterInitialization()` 方法。
9. 此时 Bean 已经准备就绪，可以被应用程序使用了。他们将一直驻留在 ApplicationContext 中，直到 ApplicationContext 被销毁。
10. 如果 Bean 实现了 `DisposableBean` 接口，Spring 将调用它的 `destory()` 接口方法，同样，如果 Bean 使用了 destory-method 声明销毁方法，该方法也会被调用。

### 2.3. 完整生命周期

**如图：**

![ Spring Bean 完整生命周期 ](D:\flies\Spring\images\spring-bean-完整生命周期01.jpg)

**描述：**

1. `BeanNameAware.setBeanName()`  在创建此 Bean 的 Bean 工厂中设置 Bean 的名称，在普通属性设置之后调用，在 `InitializinngBean.afterPropertiesSet()` 方法之前调用。

2. `BeanClassLoaderAware.setBeanClassLoader()` : 在普通属性设置之后，`InitializingBean.afterPropertiesSet()` 之前调用。

3. `BeanFactoryAware.setBeanFactory()` : 回调提供了自己的 Bean 实例工厂，在普通属性设置之后，在  `InitializingBean.afterPropertiesSet()` 或者自定义初始化方法之前调用。

4. `EnvironmentAware.setEnvironment()`: 设置 environment 在组件使用时调用。

5. `EmbeddedValueResolverAware.setEmbeddedValueResolver()`: 设置 StringValueResolver 用来解决嵌入式的值域问题。

6. `ResourceLoaderAware.setResourceLoader()`: 在普通bean对象之后调用，在 `afterPropertiesSet()`  或者自定义的init-method 之前调用，在 `ApplicationContextAware` 之前调用。

7. `ApplicationEventPublisherAware.setApplicationEventPublisher()`: 在普通 Bean 属性之后调用，在初始化调用`afterPropertiesSet()` 或者自定义初始化方法之前调用。在 ApplicationContextAware 之前调用。

8. `MessageSourceAware.setMessageSource()`: 在普通bean属性之后调用，在初始化调用 `afterPropertiesSet()`  或者自定义初始化方法之前调用，在 ApplicationContextAware 之前调用。

9. `ApplicationContextAware.setApplicationContext()` :  在普通Bean对象生成之后调用，在 `InitializingBean.afterPropertiesSet()` 之前调用或者用户自定义初始化方法之前。在 `ResourceLoaderAware.setResourceLoader()`，`ApplicationEventPublisherAware.setApplicationEventPublisher`，`MessageSourceAware`之后调用。

10. `ServletContextAware.setServletContext()` : 运行时设置 ServletContext ，在普通bean初始化后调用，在 `InitializingBean.afterPropertiesSet()` 之前调用，在 `ApplicationContextAware` 之后调用**注：是在WebApplicationContext 运行时**。

11. `BeanPostProcessor.postProcessBeforeInitialization() ` :  将此 `BeanPostProcessor` 应用于给定的新 Bean 实例  在任何 Bean 初始化回调方法（像是 `InitializingBean.afterPropertiesSet()` 或者自定义的初始化方法）之前调用。这个 Bean 将要准备填充属性的值。返回的 Bean 示例可能被普通对象包装，默认实现返回是一个 Bean 。

12. `InitializingBean.afterPropertiesSet()` : 被 `BeanFactory` 在设置所有 Bean 属性之后调用（并且满足 `BeanFactory` 和 `ApplicationContextAware` ）。

13. `DestructionAwareBeanPostProcessor.postProcessBeforeDestruction()`: 在销毁之前将此 `BeanPostProcessor` 应用于给定的 Bean 实例。能够调用自定义回调，像是 DisposableBean 的销毁和自定义销毁方法，这个回调仅仅适用于工厂中的单例 Bean （包括内部bean）。

## 3. Bean 后置处理器

### 3.1. BeanPostProcessor  接口

**定义：** Bean 后置处理器允许在调用初始化方法前后对 Bean 进行额外的处理。`BeanPostProcessor `接口定义回调方法，你可以实现该方法来提供自己的实例化逻辑，依赖解析逻辑等。你也可以在 `Spring` 容器通过插入一个或多个 `BeanPostProcessor` 的实现来完成实例化，配置和初始化一个`bean`之后实现一些自定义逻辑回调方法。你可以配置多个 `BeanPostProcessor `接口，通过设置 `BeanPostProcessor `实现的` Ordered `接口提供的` order` 属性来控制这些` BeanPostProcessor` 接口的执行顺序。`BeanPostProcessor` 可以对` bean`（或对象）实例进行操作，这意味着 `Spring IoC` 容器实例化一个 `bean` 实例，然后 `BeanPostProcessor` 接口进行它们的工作。

**注意：**`ApplicationContext` 会自动检测由 `BeanPostProcessor` 接口的实现定义的 `bean`，注册这些` bean` 为后置处理器，然后通过在容器中创建` bean`，在适当的时候调用它。

### 3.2. 使用 BeanPostProcessor  接口

**用法：**自定义`BeanPostProcesso` 接口实现类，实现 `BeanPostProcesso` 接口 `BeanPostProcessor.postProcessBeforeInitialization(Object object, String beanName)` 和 `BeanPostProcessor.postProcessAfterInitialization(Object object, String beanName)`，注意命名要准确。否则会出现： `“ The type InitHelloWorld must implement the inherited  abstract method  BeanPostProcessor.postProcessBeforeInitialization(Object, String) ”`之类的错误。

``` java
// 出处 https://www.w3cschool.cn/wkspring/xs181ici.html
package com.tutorialspoint;
import org.springframework.beans.factory.config.BeanPostProcessor;
import org.springframework.beans.BeansException;
public class InitHelloWorld implements BeanPostProcessor {
   public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
      System.out.println("BeforeInitialization : " + beanName);
      return bean;  // you can return any other object as well
   }
   public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
      System.out.println("AfterInitialization : " + beanName);
      return bean;  // you can return any other object as well
   }
}
```

## 4. Bean 定义继承

**描述：**bean 定义可以包含很多的配置信息，包括构造函数的参数，属性值，容器的具体信息例如初始化方法，静态工厂方法名，等等。子 bean 的定义继承父定义的配置数据。子定义可以根据需要重写一些值，或者添加其他值。Spring Bean 定义的继承与 Java 类的继承无关，但是继承的概念是一样的。你可以定义一个父 bean 的定义作为模板和其他子 bean 就可以从父 bean 中继承所需的配置。当你使用基于 XML 的配置元数据时，通过使用父属性，指定父 bean 作为该属性的值来表明子 bean 的定义。

### 4.1. Bean 定义模板

**定义：**你可以创建一个 Bean 定义模板，不需要花太多功夫它就可以被其他子 bean 定义使用。在定义一个 Bean 定义模板时，你不应该指定**类**的属性，而应该指定带 **true** 值的**抽象**属性。

