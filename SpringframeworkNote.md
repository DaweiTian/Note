# spring框架快速上手

> spring 框架是由于软件开发的复杂性而创建的。spring 使用的是基本的 JavaBean 来完成以前只可能由EJB完成的事情。然而，spring 的用途不仅仅限于服务器端的开发。从简单性、可测试性和松耦合性角度而言，绝大部分 Java 应用都可以从 spring 中受益。——百度百科

spring是分层的JavaSE/EE应用full-stack（全栈的）轻量级开源框架，它有两个核心思想：**IoC**（Inverse of Control，控制反转）和 **AOP**（Aspect Oriented Programming，面向切面编程），提供了展现层 springMVC 和持久层 spring JDBC 以及业务层事务管理等众多的企业级应用技术，还能整合开源世界众多著名的第三方框架和类库，现在逐渐成为最受欢迎的JavaEE企业开源开发框架。

## 为什么使用spring框架——spring框架的优点

1. **便于程序间解耦，使得开发更简洁**

   spring的核心之一，IoC容器，可以将对象间的依赖关系交给spring进行控制，可以避免程序间的过度耦合。使得程序员不用再去编写关于单例模式类、解析属性文件等底层代码，而是将注意力放在上层的应用。

2. **支持面向切面编程（AOP）**

   spring的第二个核心，AOP，方便进行面向切面的编程，可以轻松的实现很多不容易用传统面向对象（OOP）实现的功能。

3. **支持声明式事务**

   支持通过声明的方式灵活的进行事务的管理，提高开发效率和质量。

4. **方便测试程序**

   支持使用非容器依赖的编程方式进行几乎所有的测试工作。

5. **支持集成各种优秀开发框架**

   spring降低了各种框架的使用难度，提供了对各种优秀框架的直接支持，如（Hibenate，Mybatis等）

6. **降低了JavaEE API的使用难度**

   它对JavaEE API进行了薄薄的封装，使得这些API的使用难度大大降低。

## 程序的耦合与解耦

> 耦合性(Coupling)，也叫做耦合度，是对模块间关联程度的度量，模块间的耦合度是指模块间的依赖关系，包括控制关系，调用关系，数据传递关系，模块间的联系越多，那么这些模块间的耦合性就越强，独立性越差；耦合的强弱取决于模块间接口的复杂性，调用模块的方式以及通过界面传送数据的大小。在软件工程中，对象之间的耦合度越高，维护的成本越高，因此对象的设计应该使得类和构建之间的耦合度最小，在软件设计中通常用耦合度和内聚度作为衡量模块独立程度的标准，划分模块的一个准则就是**高内聚低耦合**。

### 耦合的分类

    （1）内容耦合：当一个模块直接修改或操作另一个模块中的数据时，或一个模块不通过正常入口而转入另一个模块时，这样的耦合被称为内容耦合。内容耦合是最高程度的耦合，应该避免使用。
    （2）公共耦合：两个或两个以上的模块共同引用一个全局数据项，这种耦合被称为公共耦合。在具有大量公共耦合的结构中，确定究竟是哪个模块给全局变量赋了一个特定的值是十分困难的。
    （3）外部耦合：一组模块都访问同一全局简单变量而不是同一全局数据结构，而且不是通过参数表传递该全局变量的信息，则称之为外部耦合。
    （4）控制耦合：一个模块通过接口向另一个模块传递一个控制信号，接受信号的模块根据信号值而进行适当的动作，这种耦合被称为控制耦合。
    （5）标记耦合：若一个模块A通过接口向两个模块B和C传递一个公共参数，那么称模块B和C之间存在一个标记耦合。
    （6）数据耦合：模块之间通过参数来传递数据，那么被称为数据耦合。数据耦合是最低的一种耦合形式，系统中一般都存在这种类型的耦合，因为为了完成一些有意义的功能，往往需要将某些模块的输出数据作为另一些模块的输入数据。
    （7）非直接耦合：两个模块之间没有直接关系，它们之间的联系完全是通过主模块的控制和调用来实现的。
程序设计讲究的是低耦合，高内聚。就是同一个模块内的各个元素之间要高度紧密，但是各个模块之间的相互依存度却不要太紧密。如果模块间必须存在耦合，就尽量使用数据耦合，少用控制耦合，限制公共耦合的范围，尽量避免使用内容耦合。

### 以往一些代码中的耦合现象

JDBC中注册驱动时

`DriverManager.registerDriver(new com.mysql.cj.jdbc.Driver());`

通过new关键字来获取一个Driver对象，这个时候，如果没有com.mysql.cj.jdbc.Driver时，在编译的时候就会报错，这就说明我们这个注册驱动的代码是依赖于com.mysql.cj.jdbc.Driver类的，两者是耦合的，这是不利于维护的，比如以后我们想换一个别的牌子的数据库，就要修改源码重新编译。

后来，我们使用了反射的方式来加载驱动，`Class.forName("com.mysql.cj.jdbc.Driver");`实现了一定程度上的松耦合，但是如果我们换了别的牌子的数据库，还是要回来修改源码，因为这串字符串在这里给写死了。使用配置文件的方式可以解决这个问题。

### 使用工厂模式解耦

在开发中，当我们需要创建多个对象的时候，比如我们三层架构中的dao层和业务层，通过一个工厂类读取配置文件，将需要的几个对象提前创建好，存储起来，在后面需要用到的时候，直接从工厂里面拿就好了，这就是工厂模式的解决思路。

### 控制反转(Inversion of Control)

> 控制反转就是将创建对象的权利交给框架，是框架的一个重要特性。它包括依赖注入(Dependency Injection)和依赖查找(Dependency Lookup)。它的目的是削减计算机程序间的耦合，即接触代码中类之间的依赖关系。

在之前，我是通过new关键字来获取对象的，而使用工厂模式之后，工厂将需要的对象都存储在一个Map集合中，我们如果需要使用一个对象，就直接跟工厂要。这就是控制反转的一种体现。

## spring核心之IoC

> BeanFactory 才是 spring 容器中的顶层接口。
ApplicationContext 是它的子接口。
BeanFactory 和 ApplicationContext 的区别：创建对象的时间点不一样。
ApplicationContext：只要一读取配置文件，默认情况下就会创建对象。
BeanFactory：什么使用什么时候创建对象。

**ApplicationContext接口的实现类**

`ClassPathXmlApplicationContext`：它是从类的根路径下加载配置文件，推荐使用

`FileSystemXmlApplicationContex`：它是从磁盘路径上加载配置文件，配置文件可以在磁盘的任意位置。

`AnnotationConfigApplicationContext`：当我们使用注解配置容器对象时，需要使用此类来创建 spring 容器。它用来读取注解。

### \<bean\>标签及对象管理

bean标签是被beans标签包裹的，在其中可以定义多个，跟所有作为配置文件的xml文件一样，配置对象的xml文件也有约束，beans标签的约束为：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
 xsi:schemaLocation="http://www.springframework.org/schema/beans 
http://www.springframework.org/schema/beans/spring-beans.xsd">
</beans>
```



#### \<bean\>标签详解

作用：用来配置对象让spring来创建。

属性：**id**: 给对象提供一个在容器中的唯一标示，相当于Map中的Key值

  	**class**：写需要反射的对象的全限定类名，默认会调用默认构造方法来创建对象，所以如果要创建的类没有默认构造方法，那么就会导致对象无法创建。

​	**scope**：指定对象的作用范围。

 			==singleton：默认值，单例的对象==

​			 ==prototype：多例的对象==

​			 request：WEB 项目中,spring 创建一个 Bean 的对象,将对象存入到 request 域中

​			 session：WEB 项目中,spring 创建一个 Bean 的对象,将对象存入到 session 域中

​			 global session：WEB项目中，应用在Portlet环境.如果没有Portlet环境那么globalSession相当于session

**init-method**：指定类中的初始化方法名称，首先要在类中定义int()方法。

**destroy-method**：指定类中销毁方法名称，首先要在类中定义destroy()方法。

#### spring创建的bean的作用域及生命周期

**单例对象：scope="singleton"** 一个应用只有一个对象的实例，它的作用范围就是整个引用。

生命周期：

   对象出生：当应用加载，创建容器时，对象就被创建了。

   对象活着：只要容器在，对象一直活着。

   对象死亡：当应用卸载，销毁容器时，对象就被销毁了。

**多例对象：scope="prototype"** 每次访问对象时，都会重新创建对象实例。

生命周期：

   对象出生：当使用对象时，创建新的对象实例。

   对象活着：只要对象在使用中，就一直活着。

   对象死亡：当对象长时间不用时，被 java 的垃圾回收器回收了。

#### 实例化Bean的三种方式

1. **使用默认无参构造函数**

   它会根据默认无参构造函数来创建类对象。如果 bean 中没有默认无参构造函数，将会创建失败。

   `<bean id="dao" class="top.aerlee.dao.impl.UserDaoImpl"></bean>`

2. **spring 管理静态工厂使用静态工厂的方法创建对象**

   ```java
   import top.aerlee.service.impl.ShowServiceImpl;
   public class StaticFactory {
   public static ShowServiceImpl createService(){
   	return new ShowServiceImpl();
       } 
   }
   ```

   使用 StaticFactory 类中的静态方法 **createService()** 创建对象，并存入 spring 容器

   ​	id 属性：指定 bean 的 id，用于从容器中获取。

   ​	class 属性：指定静态工厂的全限定类名。

   ​	factory-method 属性：指定生产对象的静态方法。

   `<bean id="service" class="top.aerlee.factory.StaticFactory" factory-method="createService"></bean>`

3. **spring管理实例工厂使用实例工厂的方法创建对象**

   ```java
   import top.aerlee.service.impl.ShowServiceImpl;
   public class ServiceFactory {
   public ShowServiceImpl createService(){
   	return new ShowServiceImpl();
       } 
   }
   ```

   先把工厂的创建交给 spring 来管理，然后在使用工厂的 bean 来调用里面的方法

   factory-bean 属性：用于指定实例工厂 bean 的 id。

   factory-method 属性：用于指定实例工厂中创建对象的方法。

   `<bean id="ServiceFactory" class="top.aerlee.factory.InstanceFactory"></bean> `

   `<bean id="accountService" factory-bean="instancFactory" factory-method=" createAccountService"></bean>`

### spring的依赖注入(Dependency Injection)

>它是 spring 框架的核心IoC的具体实现。
>
>我们的程序在编写时，通过控制反转，把对象的创建交给了 spring框架，但是代码中不可能出现没有依赖的情况。IoC解耦只是降低他们的依赖关系，但不会消除。例如：我们的业务层仍会调用持久层的方法。那这种业务层和持久层的依赖关系，在使用spring之后，就让spring来维护了。简单的说，就是直接让框架把持久层对象传入业务层，而不需要我们自己去获取。

能够完成依赖注入的三种数据类型：

	- 基本数据类型和String类型
	- 其它的bean类型（在配置文件中配置的或者使用注解方式配置过的）
	- 复杂类型/集合类型

#### 通过构造函数注入

前面我们提到的第一种实例化bean对象的方式，就是默认通过默认构造方法来创建对象，如果不是使用默认构造方法来创建对象，而是使用有参数列表的构造方法，那么就需要使用**\<constructor-arg\>**标签来定义要传入的参数。

**\<constructor-arg\>的属性**：

index：指定参数在构造函数参数列表中的索引位置

type：指定参数在构造函数中的数据类型

name：指定参数在构造函数中的名称

value：用来赋值的，该属性可以赋的值有基本数据类型和String类型

ref：可以赋其它的bean类型，但必须要在配置文件中配置对应的bean标签

```java
package top.aerlee.domain;
/**
* 接口
* @author Vincent
*/
public interface People {
	void show();
}
```

```java
package top.aerlee.domain;

import java.util.Date;
/**
 * 拥有传参构造方法的一个类
 * @author Vincent
 */
public class Girl implements People{
    private String name;
    private Integer age;
    private Date birthday;
    private String address;
	
    public Girl(String name, Integer age, Date birthday, String address) {
    	this.name = name;
    	this.age = age;
    	this.birthday = birthday;
    	this.address = address;
    }
	public void show() {
		System.out.println(name + "," + age + "," + birthday + "," + address);
	}
}
```
配置文件：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
 xsi:schemaLocation="http://www.springframework.org/schema/beans 
http://www.springframework.org/schema/beans/spring-beans.xsd">
<!--bean配置文件-->
<bean id="girl" class="top.aerlee.domain.Girl">
	<constructor-arg name="name" value="Zero John"></constructor-arg>
    <constructor-arg name="age" value="25"></constructor-arg>
    <constructor-arg name="birthday" ref="now"></constructor-arg>
    <constructor-arg name="address" value="The US"></constructor-arg>
</bean>
<bean id="now" class="java.util.Date"></bean>
</beans>
```

#### 通过set方法注入

通过调用类中提供的set方法注入成员，一般通过配置文件给bean中的属性传值的时候使用该方法，在实际开发中，采用这种方式的是比较多的，这种方法涉及的标签是\<property\>标签，在实际的开发中我们使用这种方法比较多。

**\<property\>标签的属性：**

​	name：在类中set方法set后面的部分

​	ref：给属性赋值其它的bean对象

​	value：给属性赋基本数据类型和String类型

```java
package top.aerlee.domain;

import java.util.Date;
/**
 * 拥有set方法的一个类
 * @author Vincent
 */
public class Boy implements People{
    private String name;
    private Integer age;
    private Date birthday;
    private String address;
	
	public void setName(String name) {
		this.name = name;
	}

	public void setAge(Integer age) {
		this.age = age;
	}

	public void setBirthday(Date birthday) {
		this.birthday = birthday;
	}

	public void setAddress(String address) {
		this.address = address;
	}

	public void show() {
		System.out.println(name + "," + age + "," + birthday + "," + address);
	} 
}
```

配置文件：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
 xsi:schemaLocation="http://www.springframework.org/schema/beans 
http://www.springframework.org/schema/beans/spring-beans.xsd">
    
<!-- 使用set方法注入 -->
	<bean id="boy" class="top.aerlee.domain.Boy">
		<property name="name" value="Red"></property>
		<property name="age" value="26"></property>
		<property name="birthday" ref="now"></property>
		<property name="address" value="The Mars"></property>
	</bean>
	<bean id="now" class="java.util.Date"></bean>
</beans>
```

####  使用 p 名称空间注入

这种方式是通过在xml中导入p名称空间，然后使用`p:propertyName`这样的形式来注入数据，它的本质还是调用类中的**set()**方法来实现的注入功能。

这里类我们还是使用前面的Boy这个类，但是配置文件中需要导入p名称空间：在\<beans\>标签的属性中引入：

` xmlns:p="http://www.springframework.org/schema/p"`这么一行。

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
 xmlns:p="http://www.springframework.org/schema/p"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
 xsi:schemaLocation=" http://www.springframework.org/schema/beans 
http://www.springframework.org/schema/beans/spring-beans.xsd">
    
    <bean id="boy" class="top.aerlee.domain.Boy"
          p:name="Conor" p:age="24" p:birthday-ref="now" p:address="The Jupiter"/>
    <bean id="now" class="java.util.Date"></bean>
</beans>
```

#### 复杂类型/集合类型的注入

也就是给类中的集合成员传值，它也是使用了**set()**方法完成注入，不过变量的数据类型都是集合。包括Array, List, Set, Map, Properties几个集合的注入。

新建一个类CollectionInjection类：

```java
package top.aerlee.domain;

import java.util.Arrays;
import java.util.List;
import java.util.Map;
import java.util.Properties;
import java.util.Set;

/**
 * 拥有多个集合成员的类
 * 其中List结构的有：Array, List, Set
 * Map结构的有： Map, Properties
 * @author Vincent                                                           
 */
public class CollectionInjection {
	String[] array;
	List<String> list;
	Set<String> set;
	Map<String, String> map;
	Properties props;
	
	public void setArray(String[] array) {
		this.array = array;
	}
	public void setList(List<String> list) {
		this.list = list;
	}
	public void setSet(Set<String> set) {
		this.set = set;
	}
	public void setMap(Map<String, String> map) {
		this.map = map;
	}
	public void setProps(Properties props) {
		this.props = props;
	}
    
	@Override
	public String toString() {
		return "CollectionInjection [array=" + "\n"+Arrays.toString(array) +"\n"
		+ ", list=  " + list + "\n"
		+", set=" + set + "\n"
		+", map=" + map + "\n"
		+", props=" + props + "]";
	}	
}
```

在xml文件中配置：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
 xsi:schemaLocation="http://www.springframework.org/schema/beans 
http://www.springframework.org/schema/beans/spring-beans.xsd">

<!-- 注入集合数据 -->
<bean id="collectioninjection" class="top.aerlee.domain.CollectionInjection">
	<property name="array">
		<array>
			<value>Remember</value>
			<value>Let go</value>
			<value>Move on</value>
		</array>
	</property>
	<property name="list">
		<list>
			<value>Remember</value>
			<value>Let go</value>
			<value>Move on</value>
		</list>
	</property>
		<property name="set">
		<set>
			<value>Remember</value>
			<value>Let go</value>
			<value>Move on</value>
		</set>
	</property>
	<property name="map">
		<map>
			<entry key="name" value="Zero"></entry>
			<entry key="gender">
				<value>female</value>
			</entry>
		</map>
	</property>
	<property name="props">
		<props>
			<prop key="username">root</prop>
			<prop key="password">lovekey</prop>
		</props>
	</property>
</bean>
</beans>
```

> 在这里有一点需要注意的是，我们看到上面的配置文件中，在配置数组注入的时候用了\<property\>标签的子标签:<array\>，在配置List注入的时候用了\<property\>标签的子标签:<list\>，在配置Map注入的时候用了\<property\>标签的子标签：<map\>，但这些标签在相同结构的集合注入时是兼容的，比如你如果想要注入Array数组，你可以使用\<array\>，也可以使用\<list\>，还可以使用\<set\>，应为它们的注入结构相同；而Map集合跟Properties属于键值对的结构，所以它们两个的\<map\>和\<props\>标签是可以兼容使用的。

### 基于spring IoC的数据库查询案例

这里通过spring IoC来实现之前的mysql数据库查询案例

首先，我们的数据库是这样的：

|id(int)|name(varchar)|phone(int)|
|--|--|--|
|1|Zero|1559503|
|2|Sharon|155345|

#### Maven坐标pom.xml

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>top.aerlee</groupId>
  <artifactId>shogirlsAnnowithoutxml</artifactId>
  <version>0.0.1-SNAPSHOT</version>
  <packaging>jar</packaging>

  <name>shogirlsAnnowithoutxml</name>
  <url>http://maven.apache.org</url>

  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  </properties>

  <dependencies>
    <dependency>
			<groupId>junit</groupId>
			<artifactId>junit</artifactId>
			<version>4.12</version>
			<scope>test</scope>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-context</artifactId>
			<version>5.2.9.RELEASE</version>
		</dependency>
		<dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
			<version>5.1.6</version>
		</dependency>
		<dependency>
			<groupId>commons-dbutils</groupId>
			<artifactId>commons-dbutils</artifactId>
			<version>1.4</version>
		</dependency>
		<dependency>
		<groupId>c3p0</groupId>
		<artifactId>c3p0</artifactId>
		<version>0.9.1.2</version>
	</dependency>
  </dependencies>
</project>
```

#### 实体类，业务层及Dao层类

```java
package top.aerlee.domain;

import java.io.Serializable;
/**
 * 实体类
 * @author vincent
 */
public class Girl implements Serializable {
	private Integer id;
	private String name;
	private Integer phone;
	public Integer getId() {
		return id;
	}
	public void setId(Integer id) {
		this.id = id;
	}
	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}
	public Integer getPhone() {
		return phone;
	}
	public void setPhone(Integer phone) {
		this.phone = phone;
	}
	@Override
	public String toString() {
		return "Girl [id=" + id + ", name=" + name + ", phone=" + phone + "]";
	}
```

```java
package top.aerlee.service;

import java.util.List;
import top.aerlee.domain.Girl;
/**
 * 业务层接口
 * @author vincent
 */
public interface GirlService {
	List<Girl> showAll();
}
```

```java
package top.aerlee.service.impl;

import java.util.List;
import top.aerlee.dao.GirlDao;
import top.aerlee.domain.Girl;
import top.aerlee.service.GirlService;
/**
 * 业务层实现类
 * @author vincent
 */
public class GirlServiceImpl implements GirlService{

	private GirlDao dao;
	public void setDao(GirlDao dao) {
		this.dao = dao;
	}
	public List<Girl> showAll() {
		return dao.showAll();
	}
}
```

```java
package top.aerlee.dao;

import java.util.List;
import top.aerlee.domain.Girl;

/**
 * Dao层接口
 * @author vincent
 */
public interface GirlDao {
	List<Girl> showAll();
}
```

```java
package top.aerlee.dao.impl;

import java.util.List;
import org.apache.commons.dbutils.QueryRunner;
import org.apache.commons.dbutils.handlers.BeanListHandler;
import top.aerlee.dao.GirlDao;
import top.aerlee.domain.Girl;

/**
 * Dao层实现类
 * @author vincent
 *
 */
public class GirlDaoImpl implements GirlDao{
	private QueryRunner runner;
	public void setRunner(QueryRunner runner) {
		this.runner = runner;
	}
	public List<Girl> showAll() {
		List<Girl> list = null;
		try {
			list = runner.query("select * from girls", new BeanListHandler<Girl>(Girl.class));
		} catch (Exception e) {
			e.printStackTrace();
		}
		return list;
	}
}
```

#### 配置spring Ioc(编写xml配置文件)

我们打算使用ClassPathXmlApplicationContext，所以我们将xml文件放在main.java包下，命名为beans.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">
        <!-- 配置业务层对象 -->
	<bean id="service" class="top.aerlee.service.impl.GirlServiceImpl">
		<!-- 注入业务层中引用的dao对象 -->
		<property name="dao" ref="dao"></property>
	</bean>
	<!-- 配置dao层对象 -->
	<bean id="dao" class="top.aerlee.dao.impl.GirlDaoImpl">
		<!-- 注入dao层引用的QueryRunner对象 -->
		<property name="runner" ref="runner"></property>
	</bean>
	<!-- 配置QueryRunner对象，并设置为多例模式 -->
	<bean id="runner" class="org.apache.commons.dbutils.QueryRunner" scope="prototype">
		<!-- 注入数据库连接池对象-->
		<constructor-arg name="ds" ref="dataSource"></constructor-arg>
	</bean>
	<!-- 配置数据库连接池对象 -->
	<bean id="dataSource"
		class="com.mchange.v2.c3p0.ComboPooledDataSource">
		<!-- 注入数据 -->
		<property name="driverClass" value="com.mysql.jdbc.Driver"></property>
		<property name="jdbcUrl" value="jdbc:mysql://localhost:3306/girlsinfo"></property>
		<property name="user" value="数据库用户名"></property>
		<property name="password" value="密码"></property>
	</bean>
</beans>
```

最后编写测试类测试：

```java
package top.aerlee.test;

import java.util.List;
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import top.aerlee.domain.Girl;
import top.aerlee.service.GirlService;
import top.aerlee.service.impl.GirlServiceImpl;

public class TestIoC {

	@Test
	public void TestShowAll() {
		ApplicationContext aContext = new ClassPathXmlApplicationContext("bean.xml");
		GirlService service = (GirlService)aContext.getBean("service",GirlServiceImpl.class);
		List<Girl> list = service.showAll();
		for(Girl girl : list) {
			System.out.println(girl);
		}
	}
}
```

> 控制台输出结果：
>
> Girl [id=1, name=Zero, phone=1559503]
> Girl [id=2, name=Sharon, phone=155345]

## spring基于注解的IoC

无论是使用xml文件配置IoC还是使用注解的方式配置IoC，两者实现的功能都是一样的(都是为了降低程序间的耦合)，只是配置的方式不一样罢了。

在使用注解配置IoC时，除了`spring-beans-5.2.9.RELEASE.jar`, `spring-core-5.2.9.RELEASE.jar`, `spring-context-5.2.9.RELEASE.jar`, `spring-jcl-5.2.9.RELEASE.jar`四个必备的jar包外(使用xml文件配置时只需要以上四个jar包足够)，还需要一个**`spring-aop-5.2.9.RELEASE.jar`**包。

### 创建spring的xml配置文件并开启注解支持

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
xmlns:context="http://www.springframework.org/schema/context"
 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
 xsi:schemaLocation="http://www.springframework.org/schema/beans
 http://www.springframework.org/schema/beans/spring-beans.xsd
 http://www.springframework.org/schema/context
 http://www.springframework.org/schema/context/spring-context.xsd">
 
<!-- 告知 spring 创建容器时要扫描的包 --> 
<context:component-scan base-package="top.aerlee"></context:component-scan>
</beans>
```

>**注意：**当我们使用注解注入时，可以不用写**set()**方法；集合类型数据的注入无法使用注解完成，只能使用xml配置文件完成注入。

### 常用的注解

spring中的注解主要有四类：==用于创建对象的==，==用于注入数据的==，==用于改变作用范围的==，==跟对象生命周期相关的==。

#### 创建对象相关的注解

用于创建对象，相当于\<bean\>标签

##### @Component

把资源让 spring 来管理。相当于在 xml 中配置一个\<bean\>。

属性：value，用于指定bean的id，相当于\<bean>标签中的id属性，如果你不指定id属性，那么它会将类名的首字母改为小写后用作对象id。

##### @Controller

**一般用于表现层的注解**，它跟@Component是一样的，只不过提供了更加明确的语义化。

属性：value，用于指定bean的id，相当于\<bean>标签中的id属性，如果你不指定id属性，那么它会将类名的首字母改为小写后用作对象id。

##### @Service

**一般用于业务层的注解**，它跟@Component是一样的，只不过提供了更加明确的语义化。

属性：value，用于指定bean的id，相当于\<bean>标签中的id属性，如果你不指定id属性，那么它会将类名的首字母改为小写后用作对象id。

##### @Repository

**一般用于持久层的注解**，它跟@Component是一样的，只不过提供了更加明确的语义化。

属性：value，用于指定bean的id，相当于\<bean>标签中的id属性，如果你不指定id属性，那么它会将类名的首字母改为小写后用作对象id。

#### 注入数据相关的注解

用于注入数据，相当于\<property\>标签，当使用注解注入属性时，set 方法可以省略。

#####  @Autowired

自动按照类型注入，它只能注入其他的bean类型。只要容器中有唯一的一个对象类型跟要注入的属性类型匹配，就可以注入成功。如果容器中没有任何对象类型跟要注入的属性类型匹配，那么就会报错。当有多个类型匹配时，使用要注入的对象变量名称作为bean的id，在 spring容器查找，找到了也可以注入成功。找不到就报错。

##### @Qualifier

在自动按照类型注入的基础之上，再按照 Bean 的 id 注入。它在给成员字段注入时不能独立使用，必须和@Autowire 一起使用；但是给方法参数注入时，可以独立使用，不过它也只能注入其它bean类型。

属性：value：指定 bean 的 id。

##### @Resource 

直接按照 Bean 的 id 注入，它可以单独使用，不像**@Qualifier**要依托于**@Autowired**，它也只能注入其他 bean 类型。

属性：name：指定 bean 的 id。

> 在使用@Resource注解的时候，可能会遇到一些问题，需要导入一个包javax.annotation-api，但是导入之后又有jar包冲突，索性不用了，使用@Autowired和@Qualifier组合完全可以完成通过id来注入bean对象的，所以这里还是建议使用@Autowired和@Qualifier组合的方式，不要将时间浪费在解决这个问题上。

##### @Value

用于注入基本数据类型和 String 类型数据，

属性：value：用于指定值，而且它支持SpEL，即spring中的EL表达式：${表达式}

#### 用于改变作用范围的注解

相当于\<bean\>标签中的**scope**属性。

##### @Scope

指定 bean 的作用范围。

属性：value：指定范围的值。value的取值：**singleton**(常用)， **prototype**(常用)，request ，session ，globalsession跟在xml中配置是一样的。

#### 对象生命周期相关的注解

相当于\<bean\>中的**int-method**和**destroy-method**属性。

##### @PostConstruct

添加在类中的初始化方法上，用于指定初始化方法。

##### @PreDestroy

添加在类中的销毁方法上，用于指定销毁方法。

```java
package top.aerlee.dao.impl;

import org.springframework.stereotype.Repository;
import top.aerlee.dao.UserDao;
/**
 * 这里使用了Repository注解，且指定了它的id："userDao1"
 */
@Repository("userDao1")
public class UserDaoImpl implements UserDao {
	public void show() {
		System.out.println("我是第一个Dao对象");
	}
}
```

```java
package top.aerlee.dao.impl;

import org.springframework.stereotype.Repository;
import top.aerlee.dao.UserDao;

/**
 * 这里使用了Repository注解，并且没有设置value属性，即没有指定id，所以它使用了默认id："userDaoImpl2"
 */
@Repository
public class UserDaoImpl2 implements UserDao {
	public void show() {
		System.out.println("我是第二个Dao对象");
	}
}
```

```java
package top.aerlee.service.impl;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Service;
import top.aerlee.dao.UserDao;
import top.aerlee.service.ShowService;

/**
 * Component跟其他三个创建对象的注解作用是一样的，完全可以通用，不过我们还是最好按照对应的设计来用
 * 方便代码阅读。
 */
// @Component
@Service
public class ShowServiceImpl implements ShowService{
	@Autowired
	@Qualifier("userDao1")
	private UserDao userDao = null;
	public void shows() {
		userDao.show();
	}
    
}
```

```java
package top.aerlee.client;

import org.springframework.context.support.ClassPathXmlApplicationContext;
import org.springframework.stereotype.Controller;
import top.aerlee.service.ShowService;

@Controller
public class Client {
public static void main(String[] args) {
	ClassPathXmlApplicationContext aContext = new ClassPathXmlApplicationContext("daos.xml");
	ShowService service = (ShowService)aContext.getBean("showServiceImpl");
	service.shows();
	aContext.close();
	}
}
```

### 摆脱xml配置文件实现纯注解配置spring IoC

通过定义一个类，在该类上面添加相应的注解来配置之前在xml中的配置。还是基于前面我们的数据库查询案例，不过这里将使用配置类来替代xml配置文件。接口没有变化，只是在接口的实现类中，我们改用注解实现对象创建及依赖注入。

```java
package top.aerlee.dao.impl;

import java.util.List;
import org.apache.commons.dbutils.QueryRunner;
import org.apache.commons.dbutils.handlers.BeanListHandler;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Repository;
import top.aerlee.dao.GirlDao;
import top.aerlee.domain.Girl;

/**
 * Dao层实现类
 * @author vincent
 *
 */
@Repository("daoAnno")
public class GirlDaoAnnoImpl implements GirlDao{
	@Autowired
	private QueryRunner runner;
	
	public void setRunner(QueryRunner runner) {
		this.runner = runner;
	}

	public List<Girl> showAll() {
		List<Girl> list = null;
		try {
			list = runner.query("select * from girls", new BeanListHandler<Girl>(Girl.class));
		} catch (Exception e) {
			e.printStackTrace();
		}
		return list;
	}
}
```

```java
package top.aerlee.service.impl;

import java.util.List;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import top.aerlee.dao.GirlDao;
import top.aerlee.domain.Girl;
import top.aerlee.service.GirlService;
/**
 * 业务层实现类
 * @author vincent
 *
 */
@Service("serviceAnno")
public class GirlServiceAnnoImpl implements GirlService{
	@Autowired
	private GirlDao dao;
	public void setDao(GirlDao dao) {
		this.dao = dao;
	}
	public List<Girl> showAll() {
		return dao.showAll();
	}
}
```

####  @Configuration

用于指定当前类是一个 spring 配置类，当创建容器时会从该类上加载注解。获取容器时需要使用`AnnotationApplicationContext(有@Configuration注解的类.class)`。

属性:：value：用于指定配置类的字节码

```java
package springconfig;

import org.springframework.context.annotation.Configuration;
/**
 * 定义这个类是作为spring的配置类，相当于xml配置文件
 * Configuration注解的作用就是指定当前类作为配置类
 * @author Vincent
 */
@Configuration
public class springconfig {
}
```

#### @ComponentScan

用于指定spring在初始化容器时要扫描的包。作用和在spring的xml配置文件中的：

`<context:component-scan base-package="top.aerlee"/>`标签是一样的。

属性：basePackages：用于指定要扫描的包。和该注解中的 value 属性作用一样。

```java
package springconfig;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
/**
 * ComponentScan注解用于指定容器初始化时要扫描的包
 * @author Vincent
 */
@Configuration
@ComponentScan("top.aerlee")
public class springconfig {
}
```

#### @Bean

该注解只能写在方法上,表明使用此方法创建一个对象,并且放入 spring 容器。

属性：name，给当前@Bean 注解方法创建的对象指定一个名称，即 bean 的 id。

```java
package top.aerlee.configurations;

import javax.sql.DataSource;
import org.apache.commons.dbutils.QueryRunner;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import com.mchange.v2.c3p0.ComboPooledDataSource;

/**
 * 配置类
 * @author vincent
 */
@Configuration
@ComponentScan("top.aerlee")
public class springConfig {

	@Bean(name = "runner")
	public QueryRunner createRunner(DataSource datasource) {
		return new QueryRunner(datasource);
	}

	@Bean(name = "datasource")
	public DataSource createDataSource() {
		ComboPooledDataSource cds = new ComboPooledDataSource();
		try {
			cds.setDriverClass("com.mysql.jdbc.Driver");
			cds.setJdbcUrl("jdbc:mysql://localhost:3306/girlsinfo");
			cds.setUser("数据库用户名");
			cds.setPassword("密码");
			return cds;
		} catch (Exception e) {
			// TODO: handle exception
			e.printStackTrace();
		}
		return cds;
	}
}
```

在这里使用了@Bean注解，在配置类中将DataSource对象创建并且加入到了spring容器中，但是这样还是有一个问题，那就是关于数据库连接相关的配置都被写死在了配置类中，如果遇到了更换数据库连接等情况的话，还要更改源代码，是比较麻烦的。使用@@PropertySource注解可以解决这个问题。

#### @PropertySource

用于加载 .properties文件中的配置 。 例如：我们配置数据源时 , 可以把连接数据库的信properties配置文件中,就可以使用此注解指定properties配置文件的位置。
属性：value[]：用于指定properties文件位置。如果是在类路径下,需要写上"classpath:"，在前面我们讲过，如果使用@Value注解来注入数据的时候，是支持"SPEL表达式"的。

我们可以将数据库连接信息写成一个JdbcLink.properties配置文件放在类路径下，在配置文件中使用@PropertySource注解来配置。

```reStructuredText
driver=com.mysql.jdbc.Driver
url=jdbc:mysql://localhost:3306/girlsinfo
username=vincent
password=vincent12345
```

```java
package top.aerlee.configurations;

import javax.sql.DataSource;
import org.apache.commons.dbutils.QueryRunner;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.PropertySource;
import com.mchange.v2.c3p0.ComboPooledDataSource;

/**
 * 配置类
 * 在这里我们使用了@PropertySource注解将之前配置在JdbcLink.properties配置文件中的连接数据引入
 * 从而解决了前面提到的配置信息写死的问题。
 * @author vincent
 *
 */
@Configuration
@ComponentScan("top.aerlee")
@PropertySource("classpath:JdbcLink.properties")
public class springConfig2 {
	// 定义数据库连接相关信息对应的变量
	@Value("${driver}")
	private String driver;
	@Value("${url}")
	private String url;
	@Value("${username}")
	private String username;
	@Value("${password}")
	private String password;
	
	@Bean(name = "runner")
	public QueryRunner createRunner(DataSource datasource) {
		return new QueryRunner(datasource);
	}

	@Bean(name = "datasource")
	public DataSource createDataSource() {
		ComboPooledDataSource cds = new ComboPooledDataSource();
		try {
			cds.setDriverClass(driver);
			cds.setJdbcUrl(url);
			cds.setUser(username);
			cds.setPassword(password);
			return cds;
		} catch (Exception e) {
			e.printStackTrace();
		}
		return cds;
	}
}
```

#### @Import

用于导入其他配置类
属性:value[]:用于指定其他配置类的字节码。

在实际的开发中，我们需要配置的内容显然不止JDBC，为了便于维护，我们可以将配置写在多个配置类中，成为子配置类，而springConfig类就是主配置类，当我们将子配置类写好之后，在主配置类中使用@Import注解可以将子配置类引入。

```java
package top.aerlee.configurations;

import javax.sql.DataSource;
import org.apache.commons.dbutils.QueryRunner;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.PropertySource;
import com.mchange.v2.c3p0.ComboPooledDataSource;

/**
 * 子配置类，在主配置类中通过@Import注解来导入
 * @author vincent
 */
@Configuration
@PropertySource("classpath:JdbcLink.properties")
public class JdbcConfig {
	
	@Value("${driver}")
	private String driver;
	@Value("${url}")
	private String url;
	@Value("${username}")
	private String username;
	@Value("${password}")
	private String password;
	
	@Bean(name = "runner")
	public QueryRunner createRunner(DataSource datasource) {
		return new QueryRunner(datasource);
	}

	@Bean(name = "datasource")
	public DataSource createDataSource() {
		ComboPooledDataSource cds = new ComboPooledDataSource();
		try {
			cds.setDriverClass(driver);
			cds.setJdbcUrl(url);
			cds.setUser(username);
			cds.setPassword(password);
			return cds;
		} catch (Exception e) {
			e.printStackTrace();
		}
		return cds;
	}
}
```

```java
package top.aerlee.configurations;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Import;

/**
 * 主配置类
 * 在这里，我们使用了@Import注解将JdbcConfig配置类导入了主配置类中
 * @author vincent
 */
@Configuration
@ComponentScan("top.aerlee")
@Import(JdbcConfig.class)
public class springConfig {
	
}
```

#### 创建纯注解环境的容器

在使用xml或者使用半注解配置spring IoC时，我们使用了`ClassPathXmlApplicationContext`类来创建spring IoC容器，而现在我们使用纯注解的方式来配置，将使用`AnnotationConfigApplicationContext`类来创建容器,它的参数不再是配置文件，而是配置类的class。

```java
package top.aerlee.test;

import java.util.List;
import org.junit.Test;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import top.aerlee.configurations.springConfig;
import top.aerlee.domain.Girl;
import top.aerlee.service.GirlService;
import top.aerlee.service.impl.GirlServiceAnnoImpl;

public class TestIoCAnno {

	@Test
	public void TestShowAll() {
		AnnotationConfigApplicationContext aContext = new AnnotationConfigApplicationContext(springConfig.class);
		GirlService service = (GirlService)aContext.getBean("serviceAnno",GirlServiceAnnoImpl.class);
		List<Girl> list = service.showAll();
		for(Girl girl : list) {
			System.out.println(girl);
		}
		aContext.close();
	}
}
```

## spring 整合Junit测试功能

在上面的测试案例中，因为在程序运行之前我们需要创建容器，我们将创建容器的代码写在了Test方法中，这意味着，每个@Test方法中我们都要写这部分代码。在以前，我们使用@Before注解来将这部分代码提取出去；但是在实际开发中，测试工程师和软件开发工程师是两个岗位，我们想要的是直接让它自动创建容器，这就需要整合spring和Junit。Junit无法实现自动创建spring容器的功能，但是Junit提供了一个注解@Runwith，它支持将Junit的运行器改为spring提供的运行器，它可以自动创建容器。

### 配置整合环境

导入坐标，我们需要一个spring-test的jar包，和Junit4.12以上的包，同时spring-aop的jar包也是必须的。

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-test</artifactId>
    <version>5.2.9.RELEASE</version>
</dependency>
```

### 使用@RunWith 注解替换原有运行器

````java
@RunWith(springJUnit4ClassRunner.class)
public class TestIoCAnno {
}
````

### 使用@ContextConfiguration注解指定 spring 配置文件的位置

如果使用的是xml文件配置的，那么就使用locations属性指定配置文件打位置，如果使用的是注解配置的，那就使用classes属性指定配置类的classes文件。

```java
@RunWith(springJUnit4ClassRunner.class)
@ContextConfiguration(classes=springConfig.class)
public class TestIoCAnno {
}
```

```java
@RunWith(springJUnit4ClassRunner.class)
@ContextConfiguration(locations="classpath:bean.xml")
public class TestIoCAnno {
}
```

### 使用@Autowired 给测试类中的变量注入数据

```java
package top.aerlee.test;

import java.util.List;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.springJUnit4ClassRunner;
import top.aerlee.configurations.springConfig;
import top.aerlee.domain.Girl;
import top.aerlee.service.GirlService;

@RunWith(springJUnit4ClassRunner.class)
@ContextConfiguration(classes=springConfig.class)
public class TestIoCAnno {

	@Autowired
	GirlService service;
	
	@Test
	public void TestShowAll() {
		List<Girl> list = service.showAll();
		for(Girl girl : list) {
			System.out.println(girl);
		}
	}
}
```

在这里，不再需要容器对象了，因为使用spring整合Junit的方式的话，容器会自动创建，容器创建后，GirlService对象也会被创建，在这里直接使用@Autowired注解注入即可。

## spring核心之AOP

> 全称是 Aspect Oriented Programming 面向切面编程。通过[预编译](https://baike.baidu.com/item/预编译/3191547)方式和运行期间动态代理实现程序功能的统一维护的一种技术。AOP是[OOP](https://baike.baidu.com/item/OOP)的延续，是软件开发中的一个热点，也是[spring](https://baike.baidu.com/item/spring)框架中的一个重要内容，是[函数式编程](https://baike.baidu.com/item/函数式编程/4035031)的一种衍生范型。利用AOP可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的[耦合度](https://baike.baidu.com/item/耦合度/2603938)降低，提高程序的可重用性，同时提高了开发的效率。

在前面我们基于spring IoC的查询案例中，每次执行SQL语句都会从数据库连接池中获取一个connection对象，而且默认是开启了自动事务的功能，即`connection.setAutoCommit(true)`，这种操作在我们打业务层只执行一条SQL时是没有问题的，如果我们在一个业务中执行了多条SQL语句，那么这种情况是无法实现功能打。通过下面的这个转账案例可以体会到这一点。

### 转账案例

在这个案例中，是一个关于对模拟账户进行操作的案例，包括账户查询，账户更新等，在账户查询和账户更新功能中，使用前面我们的查询案例一样的方式是可以实现功能的。但是我们在这里，我们添加了一个转账的操作，转账操作至少涉及两个账户，一个转出账户，一个转入账户，涉及四个账户操作，转出账户减钱，转入账户加钱，更新转出账户，更新转入账户。要完成这个功能，要跟数据库交互四次，而我们的代码逻辑是自动提交，每次交互都会自动提交，假如程序中途报错，那么，前面已经提交完的事务就算提交完了，在异常之后的事务不会被提交，如我们在转入账户加钱的账户更新代码前手动模拟一个异常，这就会导致转出账户钱减了，但是转入账户没有加钱，钱不见了的情况。我们的解决方案是添加**事务控制**模块。

```java
List<Account> sourceAccounts = Adao.showAccountByCname(sourcename);
List<Account> targetAccounts = Adao.showAccountByCname(targetname);
if (sourceAccounts == null || sourceAccounts.size() > 1) {
throw new RuntimeException("请检查你输入的账户信息");
}
if (targetAccounts == null || targetAccounts.size() > 1) {
throw new RuntimeException("请检查你输入的账户信息");
}
Account sourceAccount = sourceAccounts.get(0);
Account targetAccount = targetAccounts.get(0);
sourceAccount.setBanlance(sourceAccount.getBanlance() - money);
targetAccount.setBanlance(targetAccount.getBanlance() + money);
Adao.updateAccount(sourceAccount);
// 手动模拟一个异常
int i = 5 / 0;
Adao.updateAccount(targetAccount);
```

数据库表accounts:

|id(int)|cname(varchar)|banlance(double)|cid(int)|
|--|--|--|--|
|1|Zero|1000.00|2|
|2|Vincent|1000.00|1|

#### 实体类Account

```java
package top.aerlee.domain;

public class Account {
	private Integer id;
	private String cname;
	private double banlance;
	private Integer cid;
	
	public Integer getId() {
		return id;
	}
	public void setId(Integer id) {
		this.id = id;
	}
	public String getCname() {
		return cname;
	}
	public void setCname(String cname) {
		this.cname = cname;
	}
	public Double getBanlance() {
		return banlance;
	}
	public void setBanlance(Double banlance) {
		this.banlance = banlance;
	}
	
	public Integer getCid() {
		return cid;
	}
	public void setCid(Integer cid) {
		this.cid = cid;
	}
	@Override
	public String toString() {
		return "Account [id=" + id + ", cname=" + cname + ", banlance=" + banlance + "]";
	}
}
```

#### 实现线程绑定的工具类ConnectionUtil

这个类的作用是将Connection对象和当前线程绑定，应为控制事务要求只有一个Connetion对象才能实现，而我们之前的操作是直接将dataSource对象传给了QueryRunner的构造器，使得每次执行SQL语句的时候，从线程池中获取一个Connetion对象，这样我们无法保证事务的开启和提交是使用的同一个Connetion对象。所以在这个工具类中，我们将Connetion对象跟线程绑定，在后面执行SQL的时候，从线程上获取Connetion对象并将它当作参数传递给方法，这样就可以保证事务的开启和提交是同一个Connetion对象来执行的。

```java
package top.aerlee.utils;

import java.sql.Connection;
import javax.sql.DataSource;

public class ConnectionUtil {

	private ThreadLocal<Connection> tLocal = new ThreadLocal<Connection>();

	private DataSource dataSource;

	public void setDataSource(DataSource dataSource) {
		this.dataSource = dataSource;
	}
	
	//从当前线程上获取一个Connection对象并返回
	public Connection getThreadConnection() {
		Connection connection = null;
		try {
			// 先从ThreadLocal上获取连接
			connection = tLocal.get();
			if (connection == null) {
				// 如果线程上没有，则从数据源中获取一个连接，并存入ThreadLocal中
				connection = dataSource.getConnection();
				tLocal.set(connection);
			}
            return connection;
		} catch (Exception e) {
			e.printStackTrace();
            throw new RuntimeException(e);
		}
		
	}
	
    /**
    * 解绑
    */
	public void removeConnection() {
		tLocal.remove();
	}
}
```

#### 事务控制类TransactionManageUtil

该工具类是用来实现事务控制的，包含事务开启，事务提交，事务回滚，事务关闭四个功能，但其实都是调用ConnetionUtil对象返回的Connection对象的对应方法来实现的。

```java
package top.aerlee.utils;

public class TransactionManageUtil {
	
	private ConnectionUtil connectionUtil;

	public void setConnectionUtil(ConnectionUtil connectionUtil) {
		this.connectionUtil = connectionUtil;
	}
	
	public void  begin() {
		try {
		connectionUtil.getThreadConnection().setAutoCommit(false);
		}catch (Exception e) {
			e.printStackTrace();
		}
	}
	
	public void commit() {
		try {
		connectionUtil.getThreadConnection().commit();;
		}catch (Exception e) {
			e.printStackTrace();
		}
	}
	
	public void rollBack() {
		try {
		connectionUtil.getThreadConnection().rollback();
		}catch (Exception e) {
			e.printStackTrace();
		}
	}
	
	public void release() {
		try {
		connectionUtil.getThreadConnection().close();
		connectionUtil.removeConnection();
		}catch (Exception e) {
			e.printStackTrace();
		}
	}
}
```

#### 业务层接口IAccountService

```java
package top.aerlee.service;

import java.util.List;
import top.aerlee.domain.Account;

public interface IAccountService {
	// 显示所有账户信息
	List<Account> showAllAccount();
	// 通过账户id查看账户
	Account showAccountById(int aid);
	// 通过用户名查找账户
	List<Account> showAccountByCname(String cname);
	// 新增账户
	void addAccount(Account account);
	// 转账操作
	void transferMoney(String sourcename, String targetname, Double money);
	// 更新账户信息
	void updateAccount(Account account);
}
```

#### 持久层接口IAccountDao

```java
package top.aerlee.dao;

import java.util.List;
import top.aerlee.domain.Account;

public interface IAccountDao {
	// 显示所有账户
	List<Account> showAllAccount();
	// 通过账户id查看账户
	Account showAccountById(int aid);
	// 通过用户名查找账户
	List<Account> showAccountByCname(String cname);
	// 新增账户
	void addAccount(Account account);
	// 更新账户信息
	void updateAccount(Account account);
}
```

#### 业务层实现类AccountServiceImpl

```java
package top.aerlee.service.impl;

import java.util.List;
import top.aerlee.dao.IAccountDao;
import top.aerlee.domain.Account;
import top.aerlee.service.IAccountService;
import top.aerlee.utils.TransactionManageUtil;

public class AccountServiceImpl implements IAccountService {

	private IAccountDao Adao;
	private TransactionManageUtil transactionManager;

	public void setTransactionManager(TransactionManageUtil transactionManager) {
		this.transactionManager = transactionManager;
	}

	public void setAdao(IAccountDao adao) {
		Adao = adao;
	}

	public List<Account> showAllAccount() {
		try {
			// 开启事务
			transactionManager.begin();
			// 执行操作
			List<Account> listsAccounts = Adao.showAllAccount();
			// 提交事务
			transactionManager.commit();
			// 返回结果
			return listsAccounts;
		} catch (Exception e) {
			// 出现异常，回滚事务
			transactionManager.rollBack();
			e.printStackTrace();
			throw new RuntimeException();
		} finally {
			// 释放资源
			transactionManager.release();
		}
	}

	public Account showAccountById(int aid) {
		try {
			// 开启事务
			transactionManager.begin();
			// 执行操作
			Account accountResult = Adao.showAccountById(aid);
			// 提交事务
			transactionManager.commit();
			// 返回结果
			return accountResult;
		} catch (Exception e) {
			// 出现异常，回滚事务
			transactionManager.rollBack();
			e.printStackTrace();
			throw new RuntimeException();
		} finally {
			// 释放资源
			transactionManager.release();
		}
	}

	public List<Account> showAccountByCname(String cname) {
		try {
			// 开启事务
			transactionManager.begin();
			// 执行操作
			List<Account> listaAccounts = Adao.showAccountByCname(cname);
			// 提交事务
			transactionManager.commit();
			// 返回结果
			return listaAccounts;
		} catch (Exception e) {
			// 出现异常，回滚事务
			transactionManager.rollBack();
			e.printStackTrace();
			throw new RuntimeException();
		} finally {
			// 释放资源
			transactionManager.release();
		}
	}

	public void addAccount(Account account) {
		try {
			// 开启事务
			transactionManager.begin();
			// 执行操作
			Adao.addAccount(account);
			// 提交事务
			transactionManager.commit();
		} catch (Exception e) {
			// 出现异常，回滚事务
			transactionManager.rollBack();
			e.printStackTrace();
		} finally {
			// 释放资源
			transactionManager.release();
		}
	}

	public void transferMoney(String sourcename, String targetname, Double money) {
		try {
			// 开启事务
			transactionManager.begin();
			// 执行操作
			List<Account> sourceAccounts = Adao.showAccountByCname(sourcename);
			List<Account> targetAccounts = Adao.showAccountByCname(targetname);
			if (sourceAccounts == null || sourceAccounts.size() > 1) {
				throw new RuntimeException("请检查你输入的账户信息");
			}
			if (targetAccounts == null || targetAccounts.size() > 1) {
				throw new RuntimeException("请检查你输入的账户信息");
			}
			Account sourceAccount = sourceAccounts.get(0);
			Account targetAccount = targetAccounts.get(0);
			sourceAccount.setBanlance(sourceAccount.getBanlance() - money);
			targetAccount.setBanlance(targetAccount.getBanlance() + money);
			Adao.updateAccount(sourceAccount);
			Adao.updateAccount(targetAccount);
			// 提交事务
			transactionManager.commit();
		} catch (Exception e) {
			// 出现异常，回滚事务
			transactionManager.rollBack();
			e.printStackTrace();
		} finally {
			// 释放资源
			transactionManager.release();
		}	
	}

	public void updateAccount(Account account) {
		try {
			// 开启事务
			transactionManager.begin();
			// 执行操作
			Adao.updateAccount(account);
			// 提交事务
			transactionManager.commit();
		} catch (Exception e) {
			// 出现异常，回滚事务
			transactionManager.rollBack();
			e.printStackTrace();
		} finally {
			// 释放资源
			transactionManager.release();
		}
	}
}
```

#### 持久层实现类AccountDaoImpl

```java
package top.aerlee.dao.impl;

import java.sql.SQLException;
import java.util.List;
import org.apache.commons.dbutils.QueryRunner;
import org.apache.commons.dbutils.handlers.BeanHandler;
import org.apache.commons.dbutils.handlers.BeanListHandler;
import top.aerlee.dao.IAccountDao;
import top.aerlee.domain.Account;
import top.aerlee.utils.ConnectionUtil;

public class AccountDaoImpl implements IAccountDao{

	private QueryRunner runner;
	private ConnectionUtil connectionUtil;
	
	
	public void setConnectionUtil(ConnectionUtil connectionUtil) {
		this.connectionUtil = connectionUtil;
	}

	public void setRunner(QueryRunner runner) {
		this.runner = runner;
	}

	public List<Account> showAllAccount() {
		List<Account> resultsList = null;
		try {
			resultsList = runner.query(connectionUtil.getThreadConnection(),"select * from accounts", new BeanListHandler<Account>(Account.class));
		} catch (SQLException e) {
			e.printStackTrace();
		}
		return resultsList;
	}

	public Account showAccountById(int aid) {
		Account resultAccount = null;
		try {
			resultAccount = runner.query(connectionUtil.getThreadConnection(),"select * from accounts where id = ?", new BeanHandler<Account>(Account.class),aid);
		} catch (SQLException e) {
			e.printStackTrace();
		}
		return resultAccount;
	}

	public List<Account> showAccountByCname(String cname) {
		List<Account> resultsList = null;
		try {
			resultsList = runner.query(connectionUtil.getThreadConnection(),"select * from accounts where cname = ?", new BeanListHandler<Account>(Account.class),cname);
		}catch (SQLException e) {
			e.printStackTrace();
		}
		return resultsList;
	}

	public void addAccount(Account account) {
		try {
			runner.update(connectionUtil.getThreadConnection(),"insert into accounts(cname,banlance,cid) values(?,?,?)", account.getCname(),account.getBanlance(),account.getCid());
		} catch (SQLException e) {
			e.printStackTrace();
		}
	}
	
	public void updateAccount(Account account) {
		try {
			runner.update(connectionUtil.getThreadConnection(),"update accounts set cname=?,banlance=?,cid=? where id=? ",account.getCname(),account.getBanlance(),account.getCid(),account.getId());
		} catch (SQLException e) {
			e.printStackTrace();
		}
	}
}
```



#### 注入配置bean.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="
        http://www.springframework.org/schema/beans https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">
	<context:component-scan
		base-package="top.aerlee.test"></context:component-scan>

	<!-- 配置业务层对象 -->
	<bean id="service"
		class="top.aerlee.service.impl.AccountServiceImpl">
		<property name="Adao" ref="Adao"></property>
		<property name="transactionManager" ref="transactionManager"></property>
	</bean>

	<!-- 配置持久层对象 -->
	<bean id="Adao" class="top.aerlee.dao.impl.AccountDaoImpl">
		<property name="runner" ref="runner"></property>
		<property name="connectionUtil" ref="connectionUtil"></property>
	</bean>

	<!-- 配置QueryRunner对象，并设置为多例模式 -->
	<bean id="runner" class="org.apache.commons.dbutils.QueryRunner"
		scope="prototype">
	</bean>
	
	<bean id="connectionUtil" class="top.aerlee.utils.ConnectionUtil">
		<property name="dataSource" ref="dataSource"></property>
	</bean>
	
	<bean id="transactionManager" class="top.aerlee.utils.TransactionManageUtil">
		<property name="connectionUtil" ref="connectionUtil"></property>
	</bean>

	<!-- 配置数据库连接池对象 -->
	<bean id="dataSource"
		class="com.mchange.v2.c3p0.ComboPooledDataSource">
		<!-- 注入数据 -->
		<property name="driverClass" value="com.mysql.jdbc.Driver"></property>
		<property name="jdbcUrl"
			value="jdbc:mysql://localhost:3306/girlsinfo"></property>
		<property name="user" value="vincent"></property>
		<property name="password" value="vincent12345"></property>
	</bean>
</beans>
```

#### 测试类TestAccountService

```java
package top.aerlee.test;

import java.util.List;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.springJUnit4ClassRunner;
import top.aerlee.domain.Account;
import top.aerlee.service.IAccountService;

@RunWith(springJUnit4ClassRunner.class)
@ContextConfiguration(locations = "classpath:bean.xml")
public class TestAccountService {
	@Autowired
	private IAccountService service;

	@Test
	public void TestaddAccount() {
		Account targetAccount = new Account();
		targetAccount.setCname("Connor");
		targetAccount.setBanlance(520.0);
		targetAccount.setCid(7);
		service.addAccount(targetAccount);
	}
	
	@Test
	public void TestShowAllAccount() {
		List<Account> allAccounts = service.showAllAccount();
		for(Account account : allAccounts) {
			System.out.println(account);
		}
	}
	
	@Test
	public void TestShowAccountById() {
		Account account = service.showAccountById(2);
		System.out.println(account);
	}
	
	@Test
	public void TestShowAccountByCname() {
		List<Account> listAccounts = null;
		listAccounts = service.showAccountByCname("wenxiao");
		for(Account account : listAccounts) {
		System.out.println(account);
		}
	}
	
	@Test
	public void TestTransferMoney() {
		service.transferMoney("Vincent", "Sharon", 120.00);
	}
}
```

#### 转账案例总结

在上面的转账案例中，我们没有使用spring 的AOP功能。我们编写了绑定线程和Connetion对象的工具类和事务控制管理的工具类，通过在将Connetion对象当作参数传递给QueryRunner执行方法的方式来保证了事务提交中Connetion对象的一致性，实现了我们想要的事务控制效果。但是，我们很容易能够发现，我们的业务层充斥了很多重复的代码，而且的注入配置也变得相当复杂，这个引用那个，那个引用这个，看起来非常混乱，而且写起来也费劲。要解决这个问题，我们可以使用动态代理技术。

### 动态代理

动态代理常用的有两种实现方式，一种是基于接口的动态代理，它使用了JDK官方提供的Proxy 类，规定被代理的类至少实现一个接口。另一种是基于子类的动态代理，它使用了第三方的Cglib类库(需要导入 asm.jar。否则会报错)，被代理类不能用 final 修饰的类。

#### 使用JDK的Proxy类实现动态代理

这种方法是基于接口的动态代理，使用了JDK官方提供的**Proxy类**(`java.lang.reflect.Proxy`)，它要求被代理的类至少实现一个接口。

实现的方式：使用**Proxy类**的`newProxyInstance()`方法来获得一个代理对象。这个方法有三个参数：

| 参数              | 代表的含义                                   |
| ----------------- | -------------------------------------------- |
| ClassLoader       | 跟被代理对象使用相同的类加载器。             |
| Interfaces        | 跟被代理对象具有相同的行为。实现相同的接口。 |
| InvocationHandler | 编写如何代理的代码。                         |

##### 经销商案例(Proxy)

```java
package top.aerlee.proxy;

/**
 * 生产厂家接口，因为基于接口的动态代理要求必须实现至少一个接口
 * @author vincent
 */
public interface IProduser {
	// 卖出产品
	public void saleProduct(float money);
}
```

```java
package top.aerlee.proxy;

/**
 * 生产厂家的实现类，包含一个方法，卖出商品的方法。
 * @author vincent
 */
public class Produser implements IProduser{

	// 收到钱，卖出商品
	public void saleProduct(float money) {
		System.out.println("卖出商品，收到：" + money);
	}
}
```

```java
package top.aerlee.proxy;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

/**
 * 客户类，从经销商处购买商品
 * 基于接口实现的动态代理
 * @author vincent
 *
 */
public class Customer {
	public static void main(String[] args) {
		// 如果定义一个匿名内部类，并且希望它使用一个在其外部定义的对象，那么编译器会要求其参数引用是final类。
		final Produser produser = new Produser();
		// 通过Proxy类获得一个动态代理对象，它有三个参数，第三个参数InvocationHandler对象是通过匿名内部类的方式实现的。
		IProduser produserProxyIProduser = (IProduser)Proxy.newProxyInstance(produser.getClass().getClassLoader(), 
				produser.getClass().getInterfaces(), new InvocationHandler() {
					
					@Override
					public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
						Object resultObj = null;
						//方法增强
						float money = (float)args[0];
						// 判断方法名是否是卖出产品的方法
						if("saleProduct".equals(method.getName())) {
							// 在这里进行对应方法的增强，抽取20%的提成
							resultObj = method.invoke(produser, money*0.8f);
						}
						return resultObj;
					}
				});
		// 使用代理对象来执行方法。
		produserProxyIProduser.saleProduct(10000f);
	}
}
```

#### 使用CGLib的Enhancer类实现动态代理

这种方式是基于子类实现的动态代理，它要求被代理对象不能是最终类。使用到了CGLib中的Enhancer类的create(Class, Callback)方法，该方法的参数:

| 参数     | 代表的含义         |
| -------- | ------------------ |
| Class    | 被代理对象的字节码 |
| Callback | 如何代理           |

##### 经销商案例(CGLib)

因为这里使用了第三方库——cglib，所以我们创建一个maven工程，并导入坐标。

```xml
<dependency>
    <groupId>cglib</groupId>
    <artifactId>cglib</artifactId>
    <version>3.3.0</version>
</dependency>
```

生产者类，不再实现任何接口

```java
package top.aerlee.proxy;

/**
 * 生产厂家的类，不再实现任何接口，包含一个方法，卖出商品的方法。
 * @author vincent
 */
public class Produser{

	// 收到钱，卖出商品
	public void saleProduct(float money) {
		System.out.println("卖出商品，收到：" + money);
	}
}
```

客户类，使用cglib实现动态代理

```java
package top.aerlee.proxy;

import java.lang.reflect.Method;
import net.sf.cglib.proxy.Enhancer;
import net.sf.cglib.proxy.MethodInterceptor;
import net.sf.cglib.proxy.MethodProxy;

/**
 * 客户类，从经销商处购买商品
 * 基于子类实现的动态代理，使用了第三方库cglib的Enhancer类
 * @author vincent
 *
 */
public class Customer {
	public static void main(String[] args) {
		Produser produser = new Produser();
		
		// 使用Enhancer类的create方法获取一个代理对象，这里MethodInterceptor实现了Callback接口
		Produser cglibProduser = (Produser)Enhancer.create(produser.getClass(), 
				new MethodInterceptor() {
			// 这里创建了一个MethodInterceptor的匿名内部类
			@Override
			// 对于这几个参数，前面几个跟基于Proxy类是一样的，最后一个参数，MethodProxy:当前执行方法的代理对象
			public Object intercept(Object obj, Method method, Object[] args, MethodProxy proxy) throws Throwable {
				Object resultObject = null;
				String methodnameString = method.getName();
				Float moneyFloat = (Float)args[0];
				if(methodnameString.equals("saleProduct")) {
					// 如果方法是卖出商品，则执行，抽取0.2的手续费
					resultObject = method.invoke(produser, moneyFloat*0.8f);
				}
				// 返回结果
				return resultObject;
			}
		});
		
		cglibProduser.saleProduct(10000f);
	}
}
```

#### 使用动态代理实现事务控制

前面我们给业务层添加了事务控制，但是同时使得我们业务层的代码变得臃肿了，而且有大量的重复代码。在这里我们使用动态代理的方式来解决这个问题。

持久层跟实体类都不用动，在前面的基础上，添加一个生成动态代理对象的类：ProxyBeanFactory。

```java
package top.aerlee.beanfactory;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;
import top.aerlee.service.IAccountService;
import top.aerlee.utils.TransactionManageUtil;

/**
 * 一个bean工厂类，用于生成Service的动态代理对象
 * @author vincent
 *
 */
public class ProxyServiceFactory {

	private IAccountService service;
	private TransactionManageUtil transactionManager;
	
	public final void setService(IAccountService service) {
		this.service = service;
	}
	public final void setTransactionManager(TransactionManageUtil transactionManager) {
		this.transactionManager = transactionManager;
	}
	
	/**
	 * 该方法获取一个动态代理业务层对象
	 * @return 动态代理业务层对象
	 */
	public IAccountService getProxyService() {
		IAccountService proxyAccountService = (IAccountService)Proxy.newProxyInstance(service.getClass().getClassLoader(), service.getClass().getInterfaces(),
				new InvocationHandler() {
					
					public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
						Object resultObject = null;
						try {
							// 开启事务
							transactionManager.begin();
							// 执行操作，我们的业务在这里实现，不过前后加了事务控制
							resultObject = method.invoke(service, args);
							// 提交事务
							transactionManager.commit();
						} catch (Exception e) {
							// 出现异常，回滚事务
							transactionManager.rollBack();
							e.printStackTrace();
							throw new RuntimeException();
						} finally {
							// 释放资源
							transactionManager.release();
						}
						
						return resultObject;
					}
				});
		// 返回代理对象
	return proxyAccountService;
	}
}
```

然后将我们的业务层代码恢复简单

```java
package top.aerlee.service.impl;

import java.util.List;
import top.aerlee.dao.IAccountDao;
import top.aerlee.domain.Account;
import top.aerlee.service.IAccountService;

public class AccountServiceImpl implements IAccountService {

	private IAccountDao Adao;

	public void setAdao(IAccountDao adao) {
		Adao = adao;
	}

	public List<Account> showAllAccount() {
		return Adao.showAllAccount();
	}

	public Account showAccountById(int aid) {
			Account accountResult = Adao.showAccountById(aid);
			return accountResult;
	}

	public List<Account> showAccountByCname(String cname) {

			List<Account> listaAccounts = Adao.showAccountByCname(cname);
			return listaAccounts;
	}

	public void addAccount(Account account) {
			Adao.addAccount(account);
	}

	public void transferMoney(String sourcename, String targetname, Double money) {
			List<Account> sourceAccounts = Adao.showAccountByCname(sourcename);
			List<Account> targetAccounts = Adao.showAccountByCname(targetname);
			if (sourceAccounts == null || sourceAccounts.size() > 1) {
				throw new RuntimeException("请检查你输入的账户信息");
			}
			if (targetAccounts == null || targetAccounts.size() > 1) {
				throw new RuntimeException("请检查你输入的账户信息");
			}
			Account sourceAccount = sourceAccounts.get(0);
			Account targetAccount = targetAccounts.get(0);
			sourceAccount.setBanlance(sourceAccount.getBanlance() - money);
			targetAccount.setBanlance(targetAccount.getBanlance() + money);
			Adao.updateAccount(sourceAccount);
			Adao.updateAccount(targetAccount);
	}

	public void updateAccount(Account account) {
			Adao.updateAccount(account);
	}
}
```

注入配置：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="
        http://www.springframework.org/schema/beans https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">
	<context:component-scan
		base-package="top.aerlee.test"></context:component-scan>
		<!-- 配置代理对象 -->
		<bean id="ProxyService" factory-bean="factory" factory-method="getProxyService"></bean>
	<!-- 配置创建业务层代理对象创建者 -->
	<bean id="factory" class="top.aerlee.beanfactory.ProxyServiceFactory">
		<property name="transactionManager" ref="transactionManager"></property>
		<property name="service" ref="service"></property>
	</bean>
	<!-- 配置业务层对象 -->
	<bean id="service"
		class="top.aerlee.service.impl.AccountServiceImpl">
		<property name="Adao" ref="Adao"></property>
	</bean>

	<!-- 配置持久层对象 -->
	<bean id="Adao" class="top.aerlee.dao.impl.AccountDaoImpl">
		<property name="runner" ref="runner"></property>
		<property name="connectionUtil" ref="connectionUtil"></property>
	</bean>

	<!-- 配置QueryRunner对象，并设置为多例模式 -->
	<bean id="runner" class="org.apache.commons.dbutils.QueryRunner"
		scope="prototype">
	</bean>
	
	<bean id="connectionUtil" class="top.aerlee.utils.ConnectionUtil">
		<property name="dataSource" ref="dataSource"></property>
	</bean>
	
	<bean id="transactionManager" class="top.aerlee.utils.TransactionManageUtil">
		<property name="connectionUtil" ref="connectionUtil"></property>
	</bean>

	<!-- 配置数据库连接池对象 -->
	<bean id="dataSource"
		class="com.mchange.v2.c3p0.ComboPooledDataSource">
		<!-- 注入数据 -->
		<property name="driverClass" value="com.mysql.jdbc.Driver"></property>
		<property name="jdbcUrl"
			value="jdbc:mysql://localhost:3306/girlsinfo"></property>
		<property name="user" value="wenxiao"></property>
		<property name="password" value="Twx306990"></property>
	</bean>
</beans>
```

### spring中的AOP

在spring框架中，也是使用了动态代理的方式实现了AOP功能，不过在这里，是使用配置的方式实现前面的功能的。我们前面说了，动态代理常用的有两种方式，那么spring使用的是哪一种方式呢？ 框架会根据目标类是否实现了接口来决定采用哪种动态代理的方式。

在学习spring中的AOP之前，有一些用到的概念需要了解。

**Joinpoint(连接点):** 所谓连接点是指那些被拦截到的点。在 spring 中,这些点指的是方法，因为 spring 只支持方法类型的连接点。
**Pointcut(切入点):** 所谓切入点是指我们要对哪些 Joinpoint 进行拦截的定义。
**Advice(通知/增强):**所谓通知是指拦截到 Joinpoint 之后所要做的事情。通知的类型有前置通知,后置通知,异常通知,最终通知,环绕通知。
**Introduction(引介):**引介是一种特殊的通知，在不修改类代码的前提下, Introduction 可以在运行期为类动态地添加一些方法或成员。
**Target(目标对象):**代理的目标对象。
**Weaving(织入):**是指把增强应用到目标对象来创建新的代理对象的过程。spring 采用动态代理织入,而 AspectJ 采用编译期织入和类装载期织入。
**Proxy(代理):**一个类被 AOP 织入增强后,就产生一个结果代理类。
**Aspect(切面):**是切入点和通知(引介)的结合。

除了前面我们使用的springframework坐标，还需要导入一个坐标

```xml
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.8.7</version>
</dependency>
```

#### 涉及到的几个标签

**\<aop:config\> ** 用于声明开始 aop 的配置，所有的AOP配置都写在这个标签体内。

**\<aop:aspect\>**  用于配置切面。属性: id：给切面提供一个唯一标识。ref：引用配置好的通知类 bean 的 id。

**\<aop:pointcut\>** 用于配置切入点表达式。就是指定对哪些类的哪些方法进行增强。
属性：expression:用于定义切入点表达式。id：用于给切入点表达式提供一个唯一标识。

**\<aop:before\>**  用于配置前置通知。指定增强的方法在切入点方法之前执行
属性： method：用于指定通知类中的增强方法名称
		   ponitcut-ref：用于指定切入点的表达式的引用
	 	  poinitcut：用于指定切入点表达式

**\<aop:after-returning\>**  用于配置后置通知
属性：	method:指定通知中方法的名称。
		 	pointct:定义切入点表达式
			 pointcut-ref:指定切入点表达式的引用

**\<aop:after-throwing\>**   用于配置异常通知,在切入点方法执行产生异常后执行。它和后置通知只能执行一个。
属性：	method:指定通知中方法的名称。
			 pointct:定义切入点表达式
		 	pointcut-ref:指定切入点表达式的引用
**\<aop:after\>**   用于配置最终通知，无论切入点方法执行时是否有异常,它都会在其后面执行。
属性：	method:指定通知中方法的名称。
			 pointct:定义切入点表达式
			 pointcut-ref:指定切入点表达式的引用
**\<aop:around\>**   用于配置环绕通知，它是 spring 框架为我们提供的一种可以在代码中手动控制增强代码什么时候执行的方式，通常情况下,环绕通知都是独立使用的。
属性：	method:指定通知中方法的名称。
			 pointct:定义切入点表达式
			 pointcut-ref:指定切入点表达式的引用

#### 切入点表达式的写法

execution(表达式)

表达式语法：`execution([修饰符] 返回值类型 包名.类名.方法名(参数))`
写法说明:
全匹配方式:
`public void top.aerlee.service.ShowImpl.display()`

访问修饰符可以省略
`void top.aerlee.service.ShowImpl.display()`

返回值可以使用\*号,表示任意返回值
`* top.aerlee.service.ShowImpl.display()`

包名可以使用\*号,表示任意包,但是有几级包,需要写几个\*
`* *.*.*.*.ShowImpl.display()`

使用..来表示当前包,及其子包
`* top..ShowImpl.display()`

类名可以使用\*号,表示任意类
`* top..*.display()`

方法名可以使用\*号,表示任意方法
`* top..*.*()`

参数列表可以使用\*,表示参数可以是任意数据类型,但是必须有参数
`* top..*.*(*)`

参数列表可以使用..表示有无参数均可,有参数可以是任意类型
`* com..*.*(..)`

全通配方式:
`* *..*.*(..)`

但是在通常我们实际的开发中，我们主要是对业务层中的方法进行增强，所以一般情况下在写切入点表达式的时候都是写在业务层实现类下面。如：

`execution(* top.aerlee.service.impl.*.*(..))`

#### 使用spring AOP改写转账案例

因为在前面，我们已经实现了通过代理的方式来实现事务控制那部分代码的抽取和复用，现在使用spring AOP技术来实现，无非是用配置的方式来替代`ProxyBeanFactory`生成动态代理对象的那个类，所以我们只需要改相当少的代码。

貌似我们只需要更改配置文件跟测试类即可。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:aop="http://www.springframework.org/schema/aop"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context 
        https://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/aop 
        https://www.springframework.org/schema/aop/spring-aop.xsd" >
    
    <!--为了方便在测试中使用注解的方式来整合spring和Junit，在test包开启注解扫描-->
	<context:component-scan
		base-package="top.aerlee.test"></context:component-scan>
		
		<!-- 配置AOP切面 -->
		<aop:config>
		<aop:aspect id="serviceaop" ref="transactionManager">
		<aop:before method="begin" pointcut-ref="pt1" />
		<aop:after-returning method="commit" pointcut-ref="pt1"/>
		<aop:after-throwing method="rollBack" pointcut-ref="pt1"/>
		<aop:after method="release" pointcut-ref="pt1"/>
		<aop:pointcut expression="execution(* top.aerlee.service.impl.*.*(..))" id="pt1"/>
		</aop:aspect>
	</aop:config>
	
	<!-- 配置业务层对象 -->
	<bean id="service"
		class="top.aerlee.service.impl.AccountServiceImpl">
		<property name="Adao" ref="Adao"></property>
	</bean>

	<!-- 配置持久层对象 -->
	<bean id="Adao" class="top.aerlee.dao.impl.AccountDaoImpl">
		<property name="runner" ref="runner"></property>
		<property name="connectionUtil" ref="connectionUtil"></property>
	</bean>

	<!-- 配置QueryRunner对象，并设置为多例模式 -->
	<bean id="runner" class="org.apache.commons.dbutils.QueryRunner"
		scope="prototype">
	</bean>
	
	<bean id="connectionUtil" class="top.aerlee.utils.ConnectionUtil">
		<property name="dataSource" ref="dataSource"></property>
	</bean>
	
	<bean id="transactionManager" class="top.aerlee.utils.TransactionManageUtil">
		<property name="connectionUtil" ref="connectionUtil"></property>
	</bean>

	<!-- 配置数据库连接池对象 -->
	<bean id="dataSource"
		class="com.mchange.v2.c3p0.ComboPooledDataSource">
		<!-- 注入数据 -->
		<property name="driverClass" value="com.mysql.jdbc.Driver"></property>
		<property name="jdbcUrl"
			value="jdbc:mysql://localhost:3306/girlsinfo"></property>
		<property name="user" value="vincent"></property>
		<property name="password" value="vincent12345"></property>
	</bean>
</beans>
```

然后修改测试类：

```java
package top.aerlee.test;

import java.util.List;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.springJUnit4ClassRunner;
import top.aerlee.domain.Account;
import top.aerlee.service.IAccountService;

@RunWith(springJUnit4ClassRunner.class)
@ContextConfiguration(locations = "classpath:bean.xml")
public class TestAccountService {
	@Autowired
	private IAccountService service;

	@Test
	public void TestaddAccount() {
		Account targetAccount = new Account();
		targetAccount.setCname("Ming Li");
		targetAccount.setBanlance(520.0);
		targetAccount.setCid(7);
		service.addAccount(targetAccount);
	}
	
	@Test
	public void TestShowAllAccount() {
		List<Account> allAccounts = service.showAllAccount();
		for(Account account : allAccounts) {
			System.out.println(account);
		}
	}
	
	@Test
	public void TestShowAccountById() {
		Account account = service.showAccountById(2);
		System.out.println(account);
	}
	
	@Test
	public void TestShowAccountByCname() {
		List<Account> listAccounts = null;
		listAccounts = service.showAccountByCname("Vincent");
		for(Account account : listAccounts) {
		System.out.println(account);
		}
	}
	
	@Test
	public void TestTransferMoney() {
		service.transferMoney("Zero", "Vincent", 120.00);
	}
}
```

#### 配置环绕通知

前面的四种配置，让我们可以轻松的将方法织入切入点中，而环绕通知就是spring为我们提供的一个可以自己定义方法增强的途径。在这里可以自己定义如何增强方法。通常情况下,环绕通知都是独立使用的。

我们将上面的案例再改为环绕通知，对转账操作中的转账金额做一些手脚。

```java
package top.aerlee.aspects;

import org.aspectj.lang.ProceedingJoinPoint;
import top.aerlee.utils.TransactionManageUtil;

public class AroundTransactionManager {
	
	// 注入事务管理器
	private TransactionManageUtil transactionManager;
	
	public void setTransactionManager(TransactionManageUtil transactionManager) {
		this.transactionManager = transactionManager;
	}
	
	// 配置环绕通知，ProceedingJoinPoint 是spring提供的一个接口，可以直接使用。
	public Object transactionAround(ProceedingJoinPoint pjp) {
		Object resultObject = null;
		try {
			// 获取参数列表
			Object[] args = pjp.getArgs();
			// 获取方法名称
			String methodName = (String)pjp.getSignature().getName();
			// 前置通知，开启事务
			transactionManager.begin();
			// 如果是转账方法的话，则手动增强一下，其他的方法则正常放行，跟其他四个通知一样的。
			if(methodName.equals("transferMoney")) {
				// 处理参数,对转账金额动手脚
				args[2] = (Double)args[2] * 0.9;
			}
			// proceed()方法表示执行方法，相当于动态代理中的method.invo
			resultObject = pjp.proceed(args);
			// 后置通知，提交事务
			transactionManager.commit();
			// 返回结果
			return resultObject;
		} catch (Throwable e) {
			// 捕获异常，回滚事务
			transactionManager.rollBack();
			throw new RuntimeException(e);
		}finally {
			// 最终通知，配置事务的释放
			transactionManager.release();
		}
	}
}
```

>这里有一点需要注意，在引入其它类时，使用spring注入的方式需要添加set方法，如果没有就会导致无法注入的错误。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:aop="http://www.springframework.org/schema/aop"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context 
        https://www.springframework.org/schema/context/spring-context.xsd
        http://www.springframework.org/schema/aop 
        https://www.springframework.org/schema/aop/spring-aop.xsd" >
        
	<context:component-scan
		base-package="top.aerlee.test"></context:component-scan>
		
		<!-- 配置AOP切面 -->
<!-- 	<aop:config>
		<aop:aspect id="serviceaop" ref="transactionManager">
		<aop:before method="begin" pointcut-ref="pt1" />
		<aop:after-returning method="commit" pointcut-ref="pt1"/>
		<aop:after-throwing method="rollBack" pointcut-ref="pt1"/>
		<aop:after method="release" pointcut-ref="pt1"/>
		<aop:pointcut expression="execution(* top.aerlee.service.impl.*.*(..))" id="pt1"/>
		</aop:aspect>
	</aop:config> -->
    <!--配置环绕通知-->
	<aop:config>
		<aop:aspect id="aroundTransaction" ref="aroundTransaction">
			<aop:pointcut expression="execution(* top.aerlee.service.impl.*.*(..))" id="pt2"/>
			<aop:around method="transactionAround" pointcut-ref="pt2"></aop:around>
		</aop:aspect>
	</aop:config>
    
    <!--配置环绕通知类的对象-->
	<bean id="aroundTransaction" class="top.aerlee.aspects.AroundTransactionManager">
		<property name="transactionManager" ref="transactionManager"></property>
	</bean>
	
	<!-- 配置业务层对象 -->
	<bean id="service"
		class="top.aerlee.service.impl.AccountServiceImpl">
		<property name="Adao" ref="Adao"></property>
	</bean>

	<!-- 配置持久层对象 -->
	<bean id="Adao" class="top.aerlee.dao.impl.AccountDaoImpl">
		<property name="runner" ref="runner"></property>
		<property name="connectionUtil" ref="connectionUtil"></property>
	</bean>

	<!-- 配置QueryRunner对象，并设置为多例模式 -->
	<bean id="runner" class="org.apache.commons.dbutils.QueryRunner"
		scope="prototype">
	</bean>
	
	<bean id="connectionUtil" class="top.aerlee.utils.ConnectionUtil">
		<property name="dataSource" ref="dataSource"></property>
	</bean>
	
	<bean id="transactionManager" class="top.aerlee.utils.TransactionManageUtil">
		<property name="connectionUtil" ref="connectionUtil"></property>
	</bean>

	<!-- 配置数据库连接池对象 -->
	<bean id="dataSource"
		class="com.mchange.v2.c3p0.ComboPooledDataSource">
		<!-- 注入数据 -->
		<property name="driverClass" value="com.mysql.jdbc.Driver"></property>
		<property name="jdbcUrl"
			value="jdbc:mysql://localhost:3306/girlsinfo"></property>
		<property name="user" value="vincent"></property>
		<property name="password" value="vincent12345"></property>
	</bean>
</beans>
```

#### 基于注解配置spring AOP

基于注解配置spring AOP，无非是通过注解的方式来配置\<aop:xxx\>标签的内容，但在在这之前，首先要在配置文件中开启对注解AOP的支持：在bean.xml配置文件中加入`<aop:aspectj-autoproxy/>`，另外，bean相关的配置注解和依赖注入相关的注解也是必须的，那是基础。

AOP相关的注解主要涉及一下几个：

**@Aspect** 这个注解使用在通知类上面，用于声明当前类为一个通知类

**@Before** 这个注解用于在通知类的增强方法上，用于声明将该方法看成是前置通知。属性 value：用于指定切入点表达式，或者切入点表达式的引用。

**@After-Returning** 这个注解用于在通知类的增强方法上，用于声明将该方法看成是后置通知。属性 value：用于指定切入点表达式，或者切入点表达式的引用。

**@After-Trowing** 这个注解用于在通知类的增强方法上，用于声明将该方法看成是异常通知。属性  value：用于指定切入点表达式，或者切入点表达式的引用。

**@After** 这个注解用于在通知类的增强方法上，用于声明将该方法看成是最终通知。属性 value：用于指定切入点表达式，或者切入点表达式的引用。

**@Around** 这个注解用于配置在环绕通知方法上，用于声明将该方法看成是环绕通知。属性 value：用于指定切入点表达式，或者切入点表达式的引用。

**@Pointcut** 这个注解用于指定切入点表达式，一般会放在一个专门定义的方法上。属性 value：用于指定切入点表达式的内容。

```java
@Pointcut("execution(* top.aerlee.service.impl.*.*(..))")
private void pt3() {}
```

注解模式下切入点表达式的引用：

```java
@Before("pt3()") //注意:一定要写括号，因为前面是定义了一个方法
public void begin() {
	System.out.println("It's a begin.....")
}
```

**@EnableAspectJAutoProxy** 当使用纯注解的方式配置spring AOP时，使用该注解开启对注解AOP的支持。

## Jdbctemplate

Jdbctemplate是spring框架中提供的一个对象，它实现了对JDBC薄薄的封装。

坐标：

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>5.2.9.RELEASE</version>
</dependency>
```

一般情况下，除了`spring-jdbc-5.2.9.RELEASE.jar`包外，还需要导入`spring-tx-5.2.9.RELEASE.jar`包，这个包是用于事务控制的，不过在导入`spring-jdbc-5.2.9.RELEASE.jar`包时，`spring-tx-5.2.9.RELEASE.jar`包也会一起自动导入，如果没有自动导入，请自己手动导入。

### Jdbctemplate对象创建

在使用该对象的时候需要一个数据源DataSource，对象创建有两种，一种是空参构造，一种是有参构造，参数传入一个数据源对象。

```java
public class JdbcTemplate extends JdbcAccessor implements JdbcOperations {
    /**
     * Construct a new JdbcTemplate for bean usage.
     * <p>Note: The DataSource has to be set before using the instance.
     * @see #setDataSource
     */
    public JdbcTemplate() {
    }

    /**
    * Construct a new JdbcTemplate, given a DataSource to obtain connections from.
    * <p>Note: This will not trigger initialization of the exception translator.
    * @param dataSource the JDBC DataSource to obtain connections from
    */
    public JdbcTemplate(DataSource dataSource) {
        setDataSource(dataSource);
        afterPropertiesSet();
    }
}
```

### 常用数据源的配置

#### C3P0数据源

```xml
<!--C3P0数据源坐标-->
<dependency>
    <groupId>c3p0</groupId>
    <artifactId>c3p0</artifactId>
    <version>0.9.1.2</version>
</dependency>
```

```xml
<!--配置模板-->
<bean id="dataSource"
      class="com.mchange.v2.c3p0.ComboPooledDataSource">
    <!-- 注入数据 -->
    <property name="driverClass" value="com.mysql.jdbc.Driver"></property>
    <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/girlsinfo"></property>
    <property name="user" value="用户名"></property>
    <property name="password" value="密码"></property>
</bean>
```

#### DBCP数据源

```xml
<!--DBCP数据源坐标-->
<dependency>
    <groupId>commons-dbcp</groupId>
    <artifactId>commons-dbcp</artifactId>
    <version>1.4</version>
</dependency>
```

```xml
<!--配置模板-->
<bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource">
    <property name="driverClassName" value="com.mysql.jdbc.Driver"></property>
    <property name="url" value="jdbc:mysql://localhost:3306/girlsinfo"></property>
    <property name="username" value="用户名"></property>
    <property name="password" value="密码"></property>
</bean>
```

#### Spring内置数据源

Spring的内置数据源就在`spring-jdbc-5.2.9.RELEASE.jar`包中，所以不需要额外导包。

```xml
<!--配置Spring内置数据源-->
<bean id="dataSource3" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
    <property name="driverClassName" value="com.mysql.jdbc.Driver"></property>
    <property name="url" value="jdbc:mysql://localhost:3306/girlsinfo"></property>
    <property name="username" value="wenxiao"></property>
    <property name="password" value="Twx306990"></property>
</bean>
```

### 案例

```java
package top.aerlee.dao.impl;

import java.util.List;
import org.springframework.jdbc.core.BeanPropertyRowMapper;
import org.springframework.jdbc.core.JdbcTemplate;
import top.aerlee.dao.GirlDao;
import top.aerlee.domain.Girl;

public class GirlDaoImpl implements GirlDao{

	private JdbcTemplate Jt;
	
	public void setJt(JdbcTemplate jt) {
		Jt = jt;
	}

	public List<Girl> showGirls() {
		return Jt.query("select * from girls", new BeanPropertyRowMapper<Girl>(Girl.class));
	}
}
```

```java
package top.aerlee.service.impl;

import java.util.List;
import top.aerlee.dao.GirlDao;
import top.aerlee.domain.Girl;
import top.aerlee.service.GirlService;

public class GirlServiceImpl implements GirlService{

	private GirlDao girldao;

	public void setGirldao(GirlDao girldao) {
		this.girldao = girldao;
	}

	public List<Girl> showGirls() {
		return girldao.showGirls();
	}
}
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
xmlns:context="http://www.springframework.org/schema/context"
 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
 xsi:schemaLocation="http://www.springframework.org/schema/beans
 http://www.springframework.org/schema/beans/spring-beans.xsd
 http://www.springframework.org/schema/context
 http://www.springframework.org/schema/context/spring-context.xsd">
 
<!-- 告知 spring 创建容器时要扫描的包 --> 
<context:component-scan base-package="top.aerlee.test"></context:component-scan>

	<!-- 配置业务层对象 -->
	<bean id="girlService" class="top.aerlee.service.impl.GirlServiceImpl">
		<property name="girldao" ref="girlDao"></property>
	</bean>
    <!-- 配置Dao对象 -->
    <bean id="girlDao" class="top.aerlee.dao.impl.GirlDaoImpl">
    	<property name="Jt" ref="Jt"></property>
    </bean>
    <bean id="Jt" class="org.springframework.jdbc.core.JdbcTemplate">
    	<constructor-arg name="dataSource" ref="dataSource3"></constructor-arg>
    </bean>
    <!-- 配置Spring内置数据源对象 -->
    <bean id="dataSource3" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
		<property name="driverClassName" value="com.mysql.jdbc.Driver"></property>
		<property name="url" value="jdbc:mysql://localhost:3306/girlsinfo"></property>
		<property name="username" value="用户名"></property>
		<property name="password" value="密码"></property>
	</bean>
</beans>
```

### JdbcDaoSupport

在前面的案例中，我们都是在dao中引入一个Jdbctemplate然后注入，而另外一种方式，就是让dao继承JdbcDaoSupport，JdbcDaoSupport 是 spring 框架为我们提供的一个类,该类中定义了一个 JdbcTemplate 对象,我们可以直接获取使用,但是要想创建该对象,需要为其提供一个数据源。

源码(节选)：

```java
public abstract class JdbcDaoSupport extends DaoSupport {

	@Nullable
	private JdbcTemplate jdbcTemplate;

	/**
	 * Set the JDBC DataSource to be used by this DAO.
	 */
	public final void setDataSource(DataSource dataSource) {
		if (this.jdbcTemplate == null || dataSource != this.jdbcTemplate.getDataSource()) {
			this.jdbcTemplate = createJdbcTemplate(dataSource);
			initTemplateConfig();
		}
	}

	/**
	 * Create a JdbcTemplate for the given DataSource.
	 * Only invoked if populating the DAO with a DataSource reference!
	 * <p>Can be overridden in subclasses to provide a JdbcTemplate instance
	 * with different configuration, or a custom JdbcTemplate subclass.
	 * @param dataSource the JDBC DataSource to create a JdbcTemplate for
	 * @return the new JdbcTemplate instance
	 * @see #setDataSource
	 */
	protected JdbcTemplate createJdbcTemplate(DataSource dataSource) {
		return new JdbcTemplate(dataSource);
	}

	/**
	 * Return the JDBC DataSource used by this DAO.
	 */
	@Nullable
	public final DataSource getDataSource() {
		return (this.jdbcTemplate != null ? this.jdbcTemplate.getDataSource() : null);
	}

	/**
	 * Set the JdbcTemplate for this DAO explicitly,
	 * as an alternative to specifying a DataSource.
	 */
	public final void setJdbcTemplate(@Nullable JdbcTemplate jdbcTemplate) {
		this.jdbcTemplate = jdbcTemplate;
		initTemplateConfig();
	}

	/**
	 * Return the JdbcTemplate for this DAO,
	 * pre-initialized with the DataSource or set explicitly.
	 */
	@Nullable
	public final JdbcTemplate getJdbcTemplate() {
		return this.jdbcTemplate;
	}
```

使用继承JdbcDaoSupport的方式

```java
package top.aerlee.dao.impl;

import java.util.List;
import org.springframework.jdbc.core.BeanPropertyRowMapper;
import org.springframework.jdbc.core.JdbcTemplate;
import top.aerlee.dao.GirlDao;
import top.aerlee.domain.Girl;

public class GirlDaoImpl extends JdbcDaoSupport implements GirlDao{

	public List<Girl> showGirls() {
		return getJdbcTemplate().query("select * from girls", new BeanPropertyRowMapper<Girl>(Girl.class));
	}
}
```

```xml
    <!-- 配置Dao对象 -->
    <bean id="girlDao" class="top.aerlee.dao.impl.GirlDaoImpl">
        
    	<property name="dataSource" ref="dataSource"></property>
    </bean>
```

## Spring中的事务控制

Spring提供了一组接口在`spring-tx-5.2.9.RELEASE.jar`包中，Spring 的事务控制都是基于 AOP 的,它既可以使用编程的方式实现,也可以使用配置的方式实现，但是在实际的开发中，我们一般使用配置的方式。

### 事务控制的API

#### PlatformTransactionManager

这是一个接口，是Spring的事务管理器，它为我们提供了常用的事务操作的方法。

|方法|含义|
|--|--|
|TransactionStatus getTransaction(TransactionDefinition definition)|获取事务的状态信息|
|void commit(TransactionStatus status)|提交事务|
|void rollback(TransactionStatus status)|回滚事务|

**org.springframework.jdbc.datasource.DataSourceTransactionManager**是该接口的实现类，在开发中，我们主要是使用这个实现类来实现事务管理器的功能。

#### TransactionDefinition

该对象是事务的定义信息对象，它为我们提供了一下方法：

| 方法                         | 含义               |
| ---------------------------- | ------------------ |
| String getName()             | 获取事务对象名称   |
| int getIsolationLevel()      | 获取事务隔离等级   |
| int getPropagationBehavior() | 获取事务传播行为   |
| int getTimeout()             | 获取事务超时时间   |
| boolean isReadOnly()         | 获取事务是否为只读 |

**事务的隔离等级**反映了事务提交并发访问时的处理态度

+ ISOLATION_DEFAULT 默认级别，归属于下列某一种
+ ISOLATION_READ_UNCOMMITTED 可以读取未提交的数据
+ ISOLATION_READ_COMMITTED 只能读取已提交的数据，解决脏读的问题，是Oracle数据库的默认级别。
+ ISOLATION_REPEATABLE_READ 是否读取其他事务提交修改后的数据，解决不可重复读的问题，是Mysql数据库的默认级别。
+ ISOLATION_SERIALIZABLE 是否读取其他事务提交添加后的数据，解决幻读的问题。

**事务的传播行为**

**REQUIRED**:如果当前没有事务,就新建一个事务,如果已经存在一个事务中,加入到这个事务中。一般的选择(默认值)
**SUPPORTS**:支持当前事务,如果当前没有事务,就以非事务方式执行(没有事务)
**MANDATORY**:使用当前的事务,如果当前没有事务,就抛出异常
**REQUERS_NEW**:新建事务,如果当前在事务中,把当前事务挂起。
**NOT_SUPPORTED**:以非事务方式执行操作,如果当前存在事务,就把当前事务挂起
**NEVER**:以非事务方式运行,如果当前存在事务,抛出异常
**NESTED**:如果当前存在事务,则在嵌套事务内执行。如果当前没有事务,则执行 REQUIRED 类似的操作。

**超时时间**

该属性的默认值为-1，意为没有超时限制，如果有的话，则以秒为单位设置。

**是否只读**

查询操作时事务最好为只读，而增删改操作的事务为REQUIERED。

#### TransacionStatus

这也是一个接口，它为我们提供了事务具体的运行状态，包含如下方法：

| 方法                       | 含义                   |
| -------------------------- | ---------------------- |
| void flush()               | 刷新事务               |
| boolean hasSavePoint()     | 获取事务是否存在存储点 |
| boolean isCompleted()      | 获取事务是否完成       |
| boolean isNewTransaction() | 获取事务是否是新的事务 |
| boolean isRollbackOnly()   | 获取事务是否回滚       |
| void setRollbackOnly()     | 设置事务回滚           |

### 配置Spring声明式事务

需要导入`spring-jdbc-5.2.9.RELEASE.jar`和`spring-tx-5.2.9.RELEASE.jar`包。在xml配置文件中需要添加需要的名称空间和约束。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:aop="http://www.springframework.org/schema/aop"
    xmlns:context="http://www.springframework.org/schema/context"
    xmlns:tx="http://www.springframework.org/schema/tx"
    xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd 
        http://www.springframework.org/schema/context 
        https://www.springframework.org/schema/context/spring-context.xsd 
        http://www.springframework.org/schema/tx 
        https://www.springframework.org/schema/tx/spring-tx.xsd 
        http://www.springframework.org/schema/aop 
        https://www.springframework.org/schema/aop/spring-aop.xsd">
</beans>
```

首先，像实体类、业务层接口、持久层接口、持久层实现类、业务层实现类都要实现准备好，跟前面的是一样的，不过事务管理器不用我们来创建了，而是使用Spring提供的`DataSourceTransactionManager`对象，其余的在bean.xml配置文件中配置。

#### 配置事务管理器

```xml
<!-- 配置一个事务管理器对象 -->
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
    <!-- 注入 DataSource -->
	<property name="dataSource" ref="dataSource"></property>
</bean>
```

#### 配置事务的通知

```xml
<!-- 事务的配置 -->
<tx:advice id="txAdvice" transaction-manager="transactionManager"></tx:advice>
<!--在该配置中通过transaction-manager属性引用了事务管理器-->
```

#### 配置事务的属性

事务的属性在 tx:advice 标签内部

```xml
<tx:attributes>
<!-- 指定方法名称:是业务核心方法-->
<tx:method name="*" read-only="false" propagation="REQUIRED"/>
<tx:method name="find*" read-only="true" propagation="SUPPORTS"/>
</tx:attributes>
```

**read-only**:是否是只读事务。默认 false,不只读。
**isolation**:指定事务的隔离级别。默认值是使用数据库的默认隔离级别。
**propagation:**指定事务的传播行为。
**timeout:**指定超时时间。默认值为:-1。永不超时。
**rollback-for:**用于指定一个异常,当执行产生该异常时,事务回滚。产生其他异常,事务不回滚。
没有默认值,任何异常都回滚。
**no-rollback-for:**用于指定一个异常,当产生该异常时,事务不回滚,产生其他异常时,事务回
滚。没有默认值,任何异常都回滚。

#### 配置AOP切入点表达式

```xml
<aop:config>
<!-- 配置切入点表达式 -->
<aop:pointcut
 expression="execution(* top.aerlee.service.impl.*.*(..)) id="pt1"/>
</aop:config>
```

#### 配置切入点表达式和事务通知的对应关系

在 aop:config 标签内部配置两者的对应关系

```xml
<aop:advisor advice-ref="txAdvice" pointcut-ref="pt1"/>
```

### 基于注解的方式配置声明式事务

首先，需要在配置文件中开启Spring对注解事务的支持。

```xml
<!-- 开启 spring 对注解事务的支持 -->
<tx:annotation-driven transaction-manager="transactionManager"/>
```

#### @Transactional注解

该注解的属性和 xml 中的属性含义一致。该注解可以出现在接口上,类上和方法上。
出现接口上,表示该接口的所有实现类都有事务支持。
出现在类上,表示类中所有方法有事务支持
出现在方法上,表示方法有事务支持。
以上三个位置的优先级:方法 > 类 > 接口

```java
@Override
@Transactional(readOnly=false,propagation=Propagation.REQUIRED)
public void insertGirl() {
}
```

#### @EnableTransactionManagement

在使用纯注解的方式配置的时候，只需要将该注解加在配置类上方即可，相当于\<tx:annotation-driven\>标签。

```java
@Configuration
@EnableTransactionManagement
public class SpringConfiguration {
//在这里面配置数据源,配置JdbcTemplate,配置事务管理器
}
```

