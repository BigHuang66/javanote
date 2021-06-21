# 1、Spring

### 1.1、简介

==Spring是一个轻量级控制反转（IoC）和面向切面（AOP）的容器框架==

spring理念：使现有的技术更加容易使用，本身是一个大杂烩，整合了现有的技术框架！

```xml
<!-- https://mvnrepository.com/artifact/org.springframework/spring-webmvc -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>5.3.5</version>
</dependency>
<!-- https://mvnrepository.com/artifact/org.springframework/spring-jdbc -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>5.3.5</version>
</dependency>


```

### 1.2、优点

* Spring是一个开源的免费的框架（容器）！
* Spring是一个轻量级、非侵入式的框架！
* 控制反转（IOC），面向切面编程（AOP）
* 控制事务的处理，对框架整合的支持

### 1.3、组成





### 1.4、拓展

Spring官网介绍：现代化的Java开发，就是基于Spring开发！

* Spring Boot
  * 一个快速开发的脚手架
  * 基于SpringBoot可以快速的开发单个微服务
  * 约定大于配置！
* Spring Cloud
  * SpringCloud是基于SpringBoot实现的

因为现在大多数公司都是在使用SpringBoot进行快速开发，学习SpringBoot的前提，需要完全掌握Spring及SpringMVC！承上启下的作用！

弊端：发展了太久，违背了原来的理念！配置十分繁琐，人称“配置地狱”



# 2、IOC理论推导

1. UserDao 接口
   
2. UserDaoimpl 实现类

   

3. UserService 业务接口

   

4. UserServiceImpl 业务实现类

代码量十分大时，修改一次的成本代价十分昂贵！

使用set接口实现，发生革命性的变化！

```java
private UserDao userdao；
    
// 利用set进行动态实现值得注入！
public void setUserDao(UserDao userDao) {
    this.userdao = userDao;
}
```

* 之前，程序是主动创建对象！控制权在程序员手里
* 使用了set注入后，程序不再具有主动性，而是变成了被动的接受对象！
* 系统的耦合性大大降低~  可以更加专注的在业务的实现上！
* 这是IOC原型！



> #### DAO层：
> DAO层叫数据访问层，全称为data access object，属于一种比较底层，比较基础的操作，具体到对于某个表的增删改查，也就是说某个DAO一定是和数据库的某一张表一一对应的，其中封装了增删改查基本操作，建议DAO只做原子操作，增删改查。
>
> #### Service层：
> Service层叫服务层，被称为服务，粗略的理解就是对一个或多个DAO进行的再次封装，封装成一个服务，所以这里也就不会是一个原子操作了，需要事物控制。
>
> #### Controler层：
> Controler负责请求转发，接受页面过来的参数，传给Service处理，接到返回值，再传给页面。

**总结：**

Dao的作用是封装对数据库的访问：增删改查，不涉及业务逻辑，只是达到按某个条件获得指定数据的要求；
而Service，则是专注业务逻辑，对于其中需要的数据库操作，都通过Dao去实现。

这样做的好处：==解耦==

Service层也不一定说必须要有，对于极小的项目而言，加了Service层，反而增加了代码量

但是**通过Service层去调用Dao层，不仅可以解耦合，而且可以增加代码的拓展性。**

### IOC本质

控制反转IOC(Inversion of Control)，是一种设计思想，DI(依赖注入)是实现IOC的一种方法，也有人认为DI只是IOC的另一种说法。没有IOC的程序中 , 我们使用面向对象编程 , 对象的创建与对象间的依赖关系完全硬编码在程序中，对象的创建由程序自己控制，控制反转后将对象的创建转移给第三方，个人认为所谓控制反转就是：获得依赖对象的方式反转了。

采用XML方式配置Bean的时候，Bean的定义信息是和实现分离的，而采用注解的方式可以把两者合为一体，Bean的定义信息直接以注解的形式定义在实现类中，从而达到了零配置的目的。

控制反转是一种通过描述（XML或注解）并通过第三方去生产或获取特定对象的方式。在Spring中实现控制反转的是IoC容器，其实现方法是依赖注入（Dependency Injection,DI）。



依赖注入：本质为利用set方法来进行注入



# 3、HelloSpring





# 4、IOC创建对象的方式

1. 默认使用无参构造实现创建对象。

   

2. 有参构造函数

   ```xml
   <!--使用参数索引来匹配-->
   <bean id="user" class="com.huang.pojo.User">
   	<constructor-arg index="0" value="123"/>
       <constructor-arg index="1" value="42"/>
   </bean>
   
   <!--使用参数类型来匹配-->
   <bean id="user" class="com.huang.pojo.User">
   	<constructor-arg type="int" value="123"/>
       <constructor-arg type="java.lang.String" value="42"/>
   </bean>
   
   <!--使用参数名来匹配-->
   <bean id="user" class="com.huang.pojo.User">
   	<constructor-arg name="year" value="1997"/>
       <constructor-arg name="book" value="math"/>
   </bean>
   ```

总结：在配置文件加载的时候，容器中管理的对象就已经初始化了！



# 5、Spring配置

### 5.1 别名

```xml
<!--别名，如果添加了别名， 我们也可以使用别名获取到这个对象-->
<alias name="user" alias="userNew"/>
```

### 5.2 Bean的配置

```xml
<!--
id : bean 的唯一标识符，也就是相当于我们学的对象名
class : bean 对象所对应的全限定名 ： 包名 + 类型
name : 也是别名，而且 name 可以同时取多个别名
-->
<bean id="userT" class="com.kuang.pojo.User" name="user2, u2 u3">
	<property name="name" value="222"/>
</bean>
```



### 5.3 import

团队开发时，可以将多个配置文件导入合并为一个

applicationContext.xml

```xml
<import resource="beans.xml"/>
<import resource="beans2.xml"/>
<import resource="beans3.xml"/>
```



# 6、依赖注入

### 6.1 构造器注入





### 6.2 Set方式注入 【重点】

* 依赖注入：Set注入！
  * 依赖：bean对象的创建依赖于容器！
  * 注入：bean对象中的所有

【环境搭建】

1. 复杂类型

   ```java
   public class Address {
       private String address;
   
       public String getAddress() {
           return address;
       }
   
       public void setAddress(String address) {
           this.address = address;
       }
   
       @Override
       public String toString() {
           return "Address{" +
                   "address='" + address + '\'' +
                   '}';
       }
   }
   
   ```

2. 真实测试对象

   ```java
   public class Student {
       private String name;
       private Address address;
       private String[] books;
       private List<String> hobbies;
       private Map<String, String> card;
       private Set<String> game;
       private String wife;
       private Properties info;
       ......
   ```

3. beans.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:cache="http://www.springframework.org/schema/cache"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
           https://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/cache http://www.springframework.org/schema/cache/spring-cache.xsd">
       <bean id="address" class="com.huang.pojo.Address">
           <property name="address" value="杭州"/>
       </bean>
   
       <bean id="student" class="com.huang.pojo.Student">
           <!--普通注入-->
           <property name="name" value="Huang"/>
           <!--Bean注入-->
           <property name="address" ref="address"/>
           <!--数组注入-->
           <property name="books">
               <array>
                   <value>红楼梦</value>
                   <value>水浒传</value>
                   <value>西游记</value>
                   <value>三国演义</value>
               </array>
           </property>
           <!--List集合-->
           <property name="hobbies">
               <list>
                   <value>学习</value>
                   <value>学习</value>
                   <value>学习</value>
               </list>
           </property>
           <!--Map-->
           <property name="card">
               <map>
                   <entry key="身份证" value="2112222222"/>
                   <entry key="银行卡" value="11111111111"/>
               </map>
           </property>
           <!--Set-->
           <property name="game">
               <set>
                   <value>LOL</value>
                   <value>红色警戒</value>
               </set>
           </property>
   
           <!--null-->
           <property name="wife">
               <null/>
           </property>
   
           <!--Properties-->
           <property name="info">
               <props>
                   <prop key="学号">191190012</prop>
                   <prop key="major">仪器科学与技术</prop>
               </props>
           </property>
       </bean>
   
   </beans>
   ```

4. 测试类

   ```java
   public class MyTest {
       @Test
       public void test01() {
           ApplicationContext context = new ClassPathXmlApplicationContext("beans.xml");
           Student student = (Student) context.getBean("student");
   
           System.out.println(student.getName());
       }
   }
   ```

   

### 6.3 拓展方式注入

```xml
<!--p命名空间注入，可以直接注入属性的值：property-->
<!-- https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-p-namespace -->
xmlns:p="http://www.springframework.org/schema/p"
<bean id ="user" class="com.huang.pojo.User" p:name="Huang" p:age="24"/>
<!--c命名空间注入，通过构造器注入：construct-args -->
xmlns:c="http://www.springframework.org/schema/c"
<bean id ="user" class="com.huang.pojo.User" c:name="Huang" c:age="24" c:address-ref/>
```



### 6.4 bean的作用域

![image-20210613112856434](https://cdn.jsdelivr.net/gh/BigHuang66/picBed@main/img/20210613112856.png)

1. 单例模式（Spring 默认机制）

   ```xml
   <bean id="user2" class="com.huang.pojo.User" c:age="18" c:name="Huang" scope="singleton"/>
   ```

2. 原型模式：每次从容器中get的时候，都会产生一个新对象！

   ```xml
   <bean id="accountService" class="com.something.DefaultAccountService" scope="prototype"/>
   ```

3. 其余的request、session、application 这些只能在web开发中使用到！

 

# 7、Bean的自动装配

* 自动装配是Spring满足bean依赖一种方式！
* Spring会在上下文中自动寻找，并自动给bean装配属性！



在Spring中有三种装配的方式：

1. 在xml中显示的配置
2. 在java中显示配置
3. 隐式的自动装配 bean【重要】

### 7.1 测试



### 7.2 ByName自动装配

```xml
<bean class="com.huang.pojo.Cat"/>
<bean class="com.huang.pojo.Dog"/>
<!--
	byName: 会自动在容器上下文中查找，和自己对象set方法后面的值对应 beanid ！
-->
<bean id="people" class="com.huang.pojo.People" autowire="byName">
	<property name="name" value="Huang"/>
</bean>
```



### 7.3 ByType自动装配

```xml
<bean class="com.huang.pojo.Cat"/>
<bean class="com.huang.pojo.Dog"/>
<!--
	byType: 会自动在容器上下文查找，和自己对象属性类型相同的bean ！
-->
<bean id="people" class="com.huang.pojo.People" autowire="byType">
	<property name="name" value="Huang"/>
</bean>
```

小结：

* byName 的时候需要保证所有bean的 id 唯一，并且这个bean需要和自动注入的属性的set方法的值一致！
* byType的时候，需要保证所有bean的class唯一，并且这个bean需要和自动注入的属性的类型一致！

### 7.4 注解实现的自动装配

JKD1.5 支持注解    Spring2.5支持注解

要使用注解须知：

1. 导入约束

2. 配置注解的支持 ==context:annotation-config==

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
           https://www.springframework.org/schema/beans/spring-beans.xsd
           http://www.springframework.org/schema/context
           https://www.springframework.org/schema/context/spring-context.xsd">
   
       <context:annotation-config/>
   
   </beans>
   ```

   **@Autowire**

   直接在属性上使用即可！也可以在set方式上使用！

   使用Autowired我们可以不用编写set方法，前提是这个自动装配的属性在 IOC（Spring） 容器中存在，且符合名字byname！

设置对象可以为空：

* Autowire 的 required 属性 为false
* @Nullable 字段标记了这个注解，说明这个字段可以为null



如果@Autowired 自动装配的环境比较复杂，自动装配无法通过一个注解【@Autowired】完成的时候，我们可以使用@Qualifier(value="xxx")去配置@Autowired的使用，指定一个唯一的 bean 对象注入！

```java
public class People {
    @Autowired
    @Qualifier(value="cat11")
    private Cat cat;
    
    @Autowired
    @Qualifier(value="dog22")
    private Dog dog;
    private String name;
}
```

@Resource 注解

```java
public class People {
    @Resource(name="cat2")
    private Cat cat;
    
    @Resource
    private Dog dog;
}
```

小结：

@Resources 和 @Autowired 的区别：

* 都是用来自动装配的，都可以放在属性字段上
* @ Autowired 通过 byType 的方式实现，而且必须要求这个对象存在 ！【常用】
* @ Resources 默认通过 byname 的方式实现，如果找不到名字，则通过byType 实现！如果两个都找不到的情况下，就报错！【常用】
* 执行顺序不同：@Autowired 通过byType 的方式实现。





# 8、使用注解开发

在Spring4 之后，需要使用注解开发，必须要保证aop的包导入了

使用注解需要导入context约束，增加注解的支持！

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">
	<!--指定要扫描的包， 这个包下的注解就会生效-->
    <context:component-scan base-packet="com.huang.pojo"/>
    <context:annotation-config/>

</beans>
```



1. **bean的注入：**

* @Compoent 组件，放在类上，说明这个类被Spring管理了，为bean
* 等价于 <bean id="user" calss="com.huang.pojo.User"/>

2. **属性的注入：**

* @Value("huang")  可以注入 属性上 或者 属性的 set方法 上

* 相当于 <property name="name" value="Huang"/>



3. 衍生的注解

   @Component 有几个衍生注解，我们在web开发中，会按照 mvc 三层架构分层！

   * dao【@Repository】
   * service 【@Service】
   * controller 【@Controller】

   这四个注解功能一样，都是为了将某个类注册到Spring 中，装配 Bean

4. 自动装配置

   ```
   @Autowired : 自动装配通过类型。 名字
   	如果Autowired不能唯一自动装配上属性，则需要通过@Qualifier(value="xxx")
   @Nullable  : 字段标记了这个注解，说明这个字段可以为null
   @Resources : 自动装配通过名字。 类型
   ```

5. 作用域

   @Scope("protortype")     // 单例 singleton  原型模式 protortype

6. 小结

   xml 与 注解：

   * xml 更加万能，适用于任何场合！维护简单方便
   * 注解 不是自己类使用不了

   xml 与 注解最佳实践：

   * xml 用来管理bean；

   * 注解只负责完成属性的注入；

   * 使用过程中，必须让注解生效，就需要开启注解的支持！

     ```xml
     <!--指定要扫描的包， 这个包下的注解就会生效-->
     <context:component-scan base-packet="com.huang.pojo"/>
     <context:annotation-config/>
     ```



# 9、使用Java的方式配置Spring

可以完全不适应Spring 的 xml 配置，完全交由Java来做！

JavaConfig是Spring的一个子项目，在Spring4之后，它成为了一个核心功能！



实体类：

```java
public class User {
    private String name;

    public String getName() {
        return name;
    }
    @Value("Huang")
    public void setName(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                '}';
    }
}
```



配置文件：

```java
@Configuration
@ComponentScan("com.huang.pojo")
public class Myconfig {

    @Bean
    public User getUser() {
        return new User(); // 返回 要注入到 bean 的对象
    }
}
```



测试类：

```java
public class Mytest {

    @Test
    public void test() {
        ApplicationContext context = new AnnotationConfigApplicationContext(Myconfig.class);
        User user = context.getBean("getUser", User.class);
        System.out.println(user.getName());

    }
}
```



这种纯 Java 的配置方式，在SpringBoot中随处可见！





# 10、代理模式

代理模式 是 SpringAOP 的底层！ 

代理模式的分类：

* 静态代理
* 动态代理 













