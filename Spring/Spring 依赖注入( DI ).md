# Spring 依赖注入 （ Dependency Injection ）

[toc]

## 1. 依赖注入

简单来说，在 Spring 创建对象的同时，为其属性赋值，称之为依赖注入,即将外部的资源和对象注入到 IoC 容器中。形象来说，组件之间依赖关系由容器在运行期决定的，即由容器动态的将某个依赖关系注入到组件之中。在程序运行时，IoC 容器通过依赖注入实现动态的向某个对象提供指定对象。

### 1.1.注入方式

#### 1.1.1. 构造器注入
**定义：** 当容器调用带有一组参数的类构造函数时，基于构造函数的 DI 就完成了，其中每个参数代表一个对其他类的依赖。

**示例：**

``` xml
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">

   <!-- Definition for textEditor bean -->
   <bean id="textEditor" class="com.tutorialspoint.TextEditor">
      <constructor-arg ref="spellChecker"/>
   </bean>

   <!-- Definition for spellChecker bean -->
   <bean id="spellChecker" class="com.tutorialspoint.SpellChecker">
   </bean>

</beans>
```

**标签属性：**

<table>
    <tr>
    	<th>属性</th>
    	<th>描述</th>
    </tr>
    <tr>
    	<td>type</td>
    	<td>指定要注入构造函数参数的数据类型，当存在多个相同数据类型的参数时会报错。</td>
    </tr>
    <tr>
    	<td>index</td>
    	<td>指定要注入构造函数的参数的索引，从 0 开始。</td>
    </tr>
    <tr>
    	<td>name</td>
    	<td>指定要注入构造函数的参数的名称。</td>
    </tr>
    <tr>
    	<td>value</td>
    	<td>注入基本数据类型和String数据类型的值。</td>
    </tr>
    <tr>
    	<td>ref</td>
    	<td>根据id注入复杂数据类型（在 IoC 容器中的 bean ）。</td>
    </tr>
</table>


**注意：**

1. 构造函数参数顺序：当构造函数存在不止一个参数时，构造函数的参数在 bean 定义中的顺序就是构造函数传入参数的顺序。

#### 1.1.2. setter()方法注入

定义：当容器调用一个无参的构造函数或一个无参的静态 factory 方法来初始化你的 bean 后，通过容器在你的 bean 上调用设值函数，基于设值函数的 DI 就完成了。

**示例：**

``` xml
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">

   <!-- Definition for textEditor bean -->
   <bean id="textEditor" class="com.tutorialspoint.TextEditor">
      <property name="spellChecker" ref="spellChecker"/>
   </bean>

   <!-- Definition for spellChecker bean -->
   <bean id="spellChecker" class="com.tutorialspoint.SpellChecker">
   </bean>

</beans>
```

**标签属性：**

<table>
    <tr>
    	<th>属性</th>
    	<th>描述</th>
    </tr>
    <tr>
    	<td>name</td>
    	<td>指定属性名称。</td>
    </tr>
    <tr>
    	<td>value</td>
    	<td>注入基本数据类型和String数据类型的值。</td>
    </tr>
    <tr>
    	<td>ref</td>
    	<td>引入复杂的数据类型和集合类型。</td>
    </tr>
    <tr>
        <td>p:name (p-namespace)</td>
    	<td>等价于 &lt;property&gt; 标签 </td>
    </tr>
</table>

**注意：**

1.  定义在基于构造函数注入和基于设值函数注入中的 Beans.xml  文件的区别。唯一的区别就是在基于构造函数注入中，使用的是 `<bean>` 标签中的`<constructor-arg>` 元素，而在基于设值函数的注入中，使用的是 `<bean>` 标签中的 `<property>`元素。
2.  如果要把把一个引用传递给一个对象，那么需要使用 标签的 `ref` 属性，而如果要直接传递一个值，那么应该使用 `value` 属性。
3. 如果有许多的设值函数方法，那么在 XML 配置文件中使用 `p-namespace`  属性.

#### 1.1.3. 注入内部 beans

**定义：** inner beans 即在其他 bean 的范围内定义的 bean，在 `<property/>` 或 `<construtor-arg>`  元素中的 `<bean />` 元素称为内部 bean

**示例：**

```xml
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">

   <bean id="outerBean" class="...">
      <property name="target">
         <bean id="innerBean" class="..."/>
      </property>
   </bean>

</beans>
```

#### 1.1.4. 注入集合

**注入集合： **

<table>
    <tr>
    	<th>元素</th>
    	<th>描述</th>
    </tr>
    <tr>
    	<td>&lt;list&gt;</td>
    	<td>注入一组允许重复的列值。（即 list ）</td>
    </tr>
    <tr>
    	<td>&lt;set&gt;</td>
    	<td>注入一组不重复的值。（即 set ）</td>
    </tr>
    <tr>
    	<td>&lt;map&gt;</td>
    	<td>注入键值对集合，键-值可以是任何类型。</td>
    </tr>
    <tr>
        <td>&lt;props&gt;</td>
    	<td>注入键值对集合，键-值只能是字符串。</td>
    </tr>
</table>

**示例：**

```xml
<?xml version="1.0" encoding="UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd">

   <!-- Definition for javaCollection -->
   <bean id="javaCollection" class="com.tutorialspoint.JavaCollection">

      <!-- results in a setAddressList(java.util.List) call -->
      <property name="addressList">
         <list>
            <value>INDIA</value>
            <value>Pakistan</value>
            <value>USA</value>
            <value>USA</value>
         </list>
      </property>

      <!-- results in a setAddressSet(java.util.Set) call -->
      <property name="addressSet">
         <set>
            <value>INDIA</value>
            <value>Pakistan</value>
            <value>USA</value>
            <value>USA</value>
        </set>
      </property>

      <!-- results in a setAddressMap(java.util.Map) call -->
      <property name="addressMap">
         <map>
            <entry key="1" value="INDIA"/>
            <entry key="2" value="Pakistan"/>
            <entry key="3" value="USA"/>
            <entry key="4" value="USA"/>
         </map>
      </property>

      <!-- results in a setAddressProp(java.util.Properties) call -->
      <property name="addressProp">
         <props>
            <prop key="one">INDIA</prop>
            <prop key="two">Pakistan</prop>
            <prop key="three">USA</prop>
            <prop key="four">USA</prop>
         </props>
      </property>

   </bean>

</beans>
```

## 2. 循环依赖

### 2.1. 原因 

### 2.2. 解决方案

